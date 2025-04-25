# Proxmox VE Homelab Ansible Automation

This project contains Ansible playbooks and roles for automating a Proxmox VE homelab infrastructure deployment and management.

## Features

### Infrastructure
- Proxmox VE post-installation configuration
- LXC container management
- VM creation and management
- Network configuration
- Storage management
- Security hardening

### Application Stacks
- **Media Server Stack**
  - qBittorrent (Download Management)
  - *Arr Suite (Media Management)
  - Jellyfin (Media Streaming)
- **Monitoring Stack** (Planned)
  - Prometheus
  - Grafana
  - Node Exporter
- **Backup Stack** (Planned)
  - Proxmox Backup Server
  - Duplicati
- **Home Automation Stack** (Planned)
  - Home Assistant
  - Node-RED
  - Mosquitto MQTT

## Prerequisites

- Proxmox VE 7.0+
- Ansible 2.12+
- Python 3.8+
- SSH access with sudo privileges
- Sufficient storage for applications
- Network connectivity between containers/VMs

## Project Structure

```
ansible-project/
├── group_vars/
│   ├── all/                    # Global variables
│   │   ├── main.yml           # Common settings
│   │   └── vault.yml          # Encrypted sensitive data
│   ├── media_servers/         # Media stack variables
│   ├── monitoring/            # Monitoring stack variables
│   └── automation/            # Home automation variables
├── inventory/
│   ├── production/
│   │   └── hosts.yml          # Production inventory
│   └── staging/
│       └── hosts.yml          # Staging inventory
├── playbooks/
│   ├── infrastructure/        # Infrastructure management
│   │   ├── setup-pve.yml     # Initial Proxmox setup
│   │   ├── network.yml       # Network configuration
│   │   └── storage.yml       # Storage configuration
│   ├── stacks/               # Application stack deployments
│   │   ├── media.yml         # Media server stack
│   │   ├── monitoring.yml    # Monitoring stack
│   │   └── automation.yml    # Home automation stack
│   └── maintenance/          # Maintenance tasks
│       ├── backup.yml        # Backup routines
│       └── updates.yml       # System updates
├── roles/
│   ├── infrastructure/       # Infrastructure roles
│   ├── media_stack/         # Media server roles
│   ├── monitoring_stack/    # Monitoring roles
│   └── automation_stack/    # Home automation roles
├── ansible.cfg              # Ansible configuration
├── requirements.yml         # Dependencies
└── README.md               # This documentation
```

## Quick Start

1. Clone this repository:
```bash
git clone <repository-url>
cd ansible-project
```

2. Install dependencies:
```bash
ansible-galaxy collection install -r requirements.yml
ansible-galaxy role install -r requirements.yml
```

3. Configure vault (for sensitive data):
```bash
echo "your-secure-password" > .vault_pass
chmod 600 .vault_pass
```

4. Configure your inventory in `inventory/production/hosts.yml`

5. Deploy infrastructure:
```bash
ansible-playbook playbooks/infrastructure/setup-pve.yml
```

6. Deploy desired stack:
```bash
# Media stack
ansible-playbook playbooks/stacks/media.yml

# Monitoring stack
ansible-playbook playbooks/stacks/monitoring.yml
```

## Stack-Specific Documentation

- [Media Stack Documentation](docs/media_stack.md)
- [Monitoring Stack Documentation](docs/monitoring_stack.md)
- [Home Automation Stack Documentation](docs/automation_stack.md)

## Security Considerations

- All sensitive data is encrypted using Ansible Vault
- SSH keys are required for authentication
- Network segmentation between stacks
- Regular security updates via maintenance playbooks
- Proper file permissions and access controls

## Maintenance

- Regular backups via backup playbooks
- Automated updates via maintenance playbooks
- Resource monitoring via monitoring stack
- Log rotation and management

## Contributing

1. Fork the repository
2. Create a feature branch
3. Submit a pull request

## License

MIT License

## Support

For support, please open an issue in the repository.
