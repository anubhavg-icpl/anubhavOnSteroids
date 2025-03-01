# anubhavOnSteroids

This repository provides an Ansible-based infrastructure configuration and deployment system. It is divided into multiple Ansible roles and playbooks that manage tasks on virtual machines, containers, and services within a anubhavg environment. Each role focuses on performing a specific series of tasks (e.g., installing tools, configuring networks, mounting NFS shares, or deploying containerized services) while the playbooks bring these roles together to configure entire systems.

## Highlights

- **Infrastructure as Code**: Ensures system configurations, packages, security, and containerized services are tracked in source control.
- **Container Deployments**: Manages containers (e.g., Wazuh, Keycloak, Guacamole) using roles that encapsulate tasks for setup and configuration. 
- **NFS Mounting**: Facilitates NFS shares for storage and data sharing among different services/hosts, using roles like `nfs_client` and `nfs_server`.
- **Security & Monitoring**: Provides Wazuh agent deployments, security groups, and indexers to monitor system events.
- **Playbooks**: Each major system or application scenario (e.g., streaming, bastion, knowledge management) has a corresponding playbook orchestrating roles.
- **Podman Role**: Helps configure various containers and set up container networks as needed.
- **Role Modularity**: Each role (e.g., `vaultwarden`, `postgre`, `blocky`, etc.) keeps tasks encapsulated, making the playbooks more maintainable.

## Basic Structure

- **roles/**  
  Houses individual roles, each handling a distinct piece of functionality (e.g., building volumes, directories, installing packages, etc.).  
  - Example: `roles/wazuh_indexer`: Manages Wazuh indexer certificate generation, configuration, and deployment.
  - Example: `roles/base_vm`: Handles basic virtual machine preparation, packages, and system adjustments.

- **playbooks/**  
  Contains overarching YAML files that reference and tie different roles together for specific use-cases or machine types (e.g., `torrent.yml` for a torrent VM, `nas.yml` for network-attached storage).

- **inventory/**  
  Declares hosts and groupings for staging or production. Here, “production” includes various sections such as `bastion`, `nas`, `torrent`, etc., each with their assigned host data.

- **templates/** and **files/**  
  Provide Jinja2 templates and static files used by some roles to configure deployed applications or system settings.

## Getting Started

1. **Install Requirements**: Configure Ansible on your control machine.  
2. **Review Inventory**: Update the hosts, IPs, and user variables in `inventory/production/hosts.yaml` to match your environment.  
3. **Check Variables**: Adjust role-specific variables in each playbook according to your host setup (e.g., paths, users, or container names).  
4. **Run a Playbook**: Use Ansible commands like:  
   ```
   ansible-playbook -i inventory/production/hosts.yaml playbooks/site.yml
   ```
   Replace `site.yml` with the specific playbook you need.

## Security and Monitoring

- A primary focus is placed on Wazuh for security monitoring:  
  - Roles like `wazuh_indexer` and `wazuh_agent` manage deployments, certificates, and ensure continuous observation.  
  - Additional security roles exist for tasks like Podman networking, firewall rules, or bootstrapping Keycloak for identity management.

## Wazuh Deployment Options

The repository supports two Wazuh deployment configurations:

### 1. Single Node Deployment
- Simple setup with one Wazuh manager instance
- Configuration in `inventory/production/host-single-wazuh.yml`
- Deploy using:
  ```bash
  ansible-playbook -i inventory/production/host-single-wazuh.yml playbooks/wazuh_deploy.yml
  ```

### 2. Cluster Deployment
- Distributed setup with master and worker nodes
- Configuration in `inventory/production/hosts_cluster.yml`
- Features:
  - One master node
  - Multiple worker nodes
  - Automatic cluster synchronization
  - Load balancing capabilities
- Deploy using:
  ```bash
  ansible-playbook -i inventory/production/hosts_cluster.yml playbooks/wazuh_deploy.yml
  ```

### Configuration Files
- `wazuh_deploy.yml`: Main playbook that detects deployment type
- `wazuh_manager_simple.yml`: Single node configuration
- `wazuh_cluster.yml`: Cluster configuration with master-worker setup

### Requirements
- Generate a unique 32-character cluster key for cluster deployment
- Ensure proper network connectivity between nodes
- Set up correct DNS resolution or hosts file entries
- Configure firewall rules for inter-node communication (ports 1515, 1516)

## Customization

All roles and variables can be customized by altering .yml files in `roles/` or adjusting top-level playbooks in `playbooks/`. The modular structure makes it straightforward to extend or modify.

## Contributing

Feel free to add roles, refine existing tasks, or improve the structure. Always test changes locally in a development environment before pushing updates or applying them to production systems.

## Usage
1. Clone this repository.
2. Update your inventory in `inventory/production/hosts.yaml` with the proper hosts.
3. Adjust variables in each role or playbook as required by your environment.
4. Run an Ansible playbook, for example:
   ```
   ansible-playbook -i inventory/production/hosts.yaml playbooks/site.yml
   ```
5. Monitor the output to ensure successful setup and configuration.
