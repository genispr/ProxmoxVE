# Proxmox Backup Server Role

This Ansible role handles the installation and PBS-specific configuration of Proxmox Backup Server (PBS). It works in conjunction with the main Proxmox role, which handles common functionality like repository management, system limits, and service management.

## Features

- Installs Proxmox Backup Server and its dependencies
- Configures PBS-specific settings
- Sets up MOTD with PBS information
- Manages subscription nag message (optional)
- Handles PBS service state

## Dependencies

This role depends on the main Proxmox role for:
- Repository management
- System limits configuration
- Service management
- Security settings

## Usage

1. First, configure the main Proxmox role with PBS settings:

```yaml
# In your group_vars or host_vars
proxmox_repositories:
  pbs:
    enterprise: false
    no_subscription: true
    test: false
    version: "{{ ansible_distribution_release }}"  # Uses host's release version

proxmox_backup:
  pbs_enabled: true  # Required to enable PBS installation
  pbs:
    remove_subscription_nag: true
    motd_enabled: true
    web_interface_port: 8007
    dependencies:
      - gpg
      - proxmox-backup-server
```

2. Include both roles in your playbook:

```yaml
- hosts: pbs_servers
  roles:
    - role: proxmox  # Must come first
    - role: proxmox_backup_server
```

## Configuration Structure

The configuration is primarily handled by the Proxmox role to maintain consistency and avoid duplication:

1. **Repository Settings** (`proxmox_repositories.pbs`):
   - Controls enterprise, no-subscription, and test repositories
   - Sets the PBS version to use

2. **Backup Configuration** (`proxmox_backup`):
   - Controls whether PBS is enabled
   - Configures PBS-specific settings

3. **Service Management** (`managed_services`):
   - Handles PBS service state
   - Automatically managed when PBS is enabled

## Post-Installation

After installation, you can access the Proxmox Backup Server web interface at:
```
https://YOUR_SERVER_IP:{{ proxmox_backup.pbs.web_interface_port }}
```

## Troubleshooting

1. If the role fails with "PBS is not enabled", ensure:
   ```yaml
   proxmox_backup:
     pbs_enabled: true
   ```

2. For repository issues, verify:
   ```yaml
   proxmox_repositories:
     pbs:
       no_subscription: true
   ```

## License

MIT

## Author Information

Created by the Proxmox community scripts team.
Original scripts by:
- tteck (tteckster)
- MickLesk
- michelroegl-brunner 
