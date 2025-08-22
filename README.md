# OpenStack Bootstrap with Ansible

This Ansible playbook automates the deployment and management of an OpenStack environment. It provisions resources such as flavors, networks, subnets, routers, keypairs, images, and compute instances. The playbook is modular, allowing for easy extension and customization through variable definitions.

## Table of Contents
- [OpenStack Bootstrap with Ansible](#openstack-bootstrap-with-ansible)
  - [Table of Contents](#table-of-contents)
  - [Overview](#overview)
  - [Installing Dependencies](#installing-dependencies)
  - [Prerequisites](#prerequisites)
  - [Playbook Structure](#playbook-structure)
  - [Configuration Details](#configuration-details)
    - [Flavors](#flavors)
    - [Networks and Subnets](#networks-and-subnets)
    - [Router](#router)
    - [Keypairs](#keypairs)
    - [Images](#images)
    - [Instances](#instances)
  - [Usage](#usage)
  - [Variables](#variables)
  - [Tags](#tags)
  - [🔗 Stay connected with DockerMe! 🚀](#-stay-connected-with-dockerme-)

## Overview
This Ansible project bootstraps an OpenStack cloud infrastructure by:
- Creating custom compute flavors.
- Setting up public and private networks with subnets.
- Configuring a router with external gateway and interfaces.
- Adding SSH keypairs.
- Downloading and uploading disk images (e.g., Cirros, Ubuntu 24.04, Debian 12).
- Deploying and deleting compute instances with custom configurations like user data, volumes, and security groups.

The main playbook (`main.yml`) imports tasks from separate YAML files for each resource type, making it organized and reusable.

## Installing Dependencies

Install the required collections using the provided `requirements.yml` file:

```bash
ansible-galaxy install -r requirements.yml
```

## Prerequisites
To run this playbook, ensure the following:
- **Ansible**: Version 2.9 or higher.
- **OpenStack Ansible Collection**: Install via `ansible-galaxy collection install openstack.cloud`.
- **OpenStack Environment**: Access to an OpenStack cloud with admin credentials (defined in `openstack_auth` variable).
- **Python Dependencies**: Required for OpenStack modules (e.g., `openstacksdk`).
- **SSH Public Keys**: Keys must be provided in the `ssh_keys` variable.
- **Image Download Access**: URLs for images must be accessible; downloads are saved to `image_path`.


Run the playbook on a control node with network access to the OpenStack API.

## Playbook Structure
```
├── main.yml                      # Main playbook importing all tasks and defining variables
├── create-flavor.yml             # Creates custom flavors
├── create-network.yml            # Creates networks and subnets
├── create-router.yml             # Creates router and adds interfaces
├── create-keypair.yml            # Adds SSH keypairs
├── create-image.yml              # Downloads and uploads images
├── create-instance.yml           # Creates compute instances with user data
└── delete-instance.yml           # Deletes compute instances
```

## Configuration Details

### Flavors
The `create-flavor.yml` task creates flavors like `Tiny`, `Small`, `Medium`, and `Large` with specified RAM, vCPUs, disk, and public visibility. Configured via the `flavors` list.

### Networks and Subnets
The `create-network.yml` task provisions networks (e.g., `Public`, `Private`, `zun_net`) with provider types (flat, vxlan), external flags, and subnets including CIDR, gateway, DHCP, DNS, and allocation pools.

### Router
The `create-router.yml` task creates a router with an external gateway and automatically adds interfaces for all non-external subnets.

### Keypairs
The `create-keypair.yml` task uploads multiple SSH public keys to OpenStack, defined in the `ssh_keys` list.

### Images
The `create-image.yml` task:
- Ensures a download directory exists.
- Downloads images from URLs (e.g., Cirros, Ubuntu, Debian).
- Uploads them to OpenStack with specified formats (qcow2, bare).

### Instances
The `create-instance.yml` task:
- Gathers IDs for images, flavors, networks, projects, and security groups.
- Creates instances with boot-from-volume, user data (cloud-init for hostname, users, packages, services, and files).
- Supports custom configurations like availability zones and nics.

The `delete-instance.yml` task safely deletes instances by ID.

## Usage
1. **Set Up Authentication**:
   Define `openstack_auth` in a secure way (e.g., via group_vars or vault). Example:
   ```yaml
   openstack_auth:
     auth_url: "https://your-openstack-auth-url:5000/v3"
     username: "admin"
     password: "your-password"
     project_name: "admin"
     user_domain_name: "Default"
     project_domain_name: "Default"
   ```

2. **Customize Variables**:
   Edit `main.yml` or use `--extra-vars` for custom flavors, networks, images, etc.

3. **Run the Playbook**:
   ```bash
   ansible-playbook main.yml -i inventory.ini
   ```
   - Use tags to run specific tasks (e.g., `ansible-playbook main.yml --tags create_instance`).

4. **Delete Resources**:
   ```bash
   ansible-playbook main.yml --tags delete_instance
   ```

## Variables
Key variables defined in `main.yml` include:
- `flavors`: List of flavor specs (name, ram, vcpus, disk, is_public).
- `networks`: List of network configs (name, external, subnet_name, cidr, gateway_ip, dns_nameservers, allocation pools, provider_network_type, provider_physical_network).
- `router`: Router name and external_network.
- `ssh_keys`: List of keypairs (name, public_key).
- `image_path`: Directory for downloaded images.
- `images_to_upload`: List of images (name, url, container_format, image_format, disk_format).
- `instance`: Instance details (image_name, keypair_name, flavor_name, availability_zone, network_name, project_name, security_groups_name, name, volume_size, hostname, default_password).

For detailed definitions, refer to `main.yml`.

## Tags
Use tags to execute specific parts:
- `preparing_openstack`: Runs flavor, network, router, keypair, and image creation.
- `create_flavor`, `create_network`, `create_router`, `create_keypair`, `create_image`: Individual preparation tasks.
- `create_instance`: Creates an instance.
- `delete_instance`: Deletes an instance.

## 🔗 Stay connected with DockerMe! 🚀

**Subscribe to our channels, leave a comment, and drop a like to support our content. Your engagement helps us create more valuable DevOps and cloud content!** 🙌

[![Site](https://img.shields.io/badge/Dockerme.ir-0A66C2?style=for-the-badge&logo=docker&logoColor=white)](https://dockerme.ir/) [![linkedin](https://img.shields.io/badge/linkedin-0A66C2?style=for-the-badge&logo=linkedin&logoColor=white)](https://www.linkedin.com/in/ahmad-rafiee/) [![Telegram](https://img.shields.io/badge/telegram-0A66C2?style=for-the-badge&logo=telegram&logoColor=white)](https://t.me/dockerme) [![YouTube](https://img.shields.io/badge/youtube-FF0000?style=for-the-badge&logo=youtube&logoColor=white)](https://youtube.com/@dockerme) [![Instagram](https://img.shields.io/badge/instagram-FF0000?style=for-the-badge&logo=instagram&logoColor=white)](https://instagram.com/dockerme)
