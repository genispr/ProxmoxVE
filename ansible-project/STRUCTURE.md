# Homelab Project Structure

```
ansible-project/
├── inventory/
│   ├── group_vars/
│   │   └── all/
│   │       ├── main.yml          # Global variables (network, storage)
│   │       └── vault.yml         # Encrypted sensitive data
│   └── hosts.yml                 # Single inventory for homelab
│
├── playbooks/
│   ├── setup/                    # Initial setup playbooks
│   │   ├── proxmox.yml          # Proxmox configuration
│   │   └── network.yml          # Network configuration
│   ├── vms/                     # VM deployment playbooks
│   │   ├── opnsense.yml         # Router/Firewall
│   │   ├── haos.yml             # Home Assistant
│   │   └── storage.yml          # NAS/Storage
│   └── containers/              # LXC container playbooks
│       ├── media.yml            # Media stack (*arr suite)
│       ├── monitoring.yml       # Monitoring stack
│       └── security.yml         # Security services
│
├── roles/
│   ├── proxmox/                 # Proxmox management
│   │   ├── defaults/
│   │   │   └── main.yml         # Default variables
│   │   ├── tasks/
│   │   │   ├── main.yml         # Task orchestration
│   │   │   ├── vm.yml           # VM management
│   │   │   ├── lxc.yml          # Container management
│   │   │   └── storage.yml      # Storage management
│   │   └── templates/           # Configuration templates
│   ├── network/                 # Network configuration
│   │   └── tasks/
│   │       ├── vlans.yml        # VLAN setup
│   │       └── bridges.yml      # Bridge configuration
│   └── apps/                    # Application roles
│       ├── media_stack/         # Arr suite (Sonarr, etc.)
│       ├── monitoring/          # Prometheus, Grafana
│       └── home_automation/     # Home Assistant
│
├── vars/
│   ├── network.yml              # Network configuration
│   ├── storage.yml              # Storage configuration
│   └── applications.yml         # Application settings
│
├── ansible.cfg                  # Ansible configuration
└── README.md                    # Project documentation
```

Key differences for homelab:
1. Simplified inventory (single environment)
2. Focus on homelab-specific services
3. Organized by infrastructure type (VMs vs Containers)
4. Integrated network and storage management
5. Emphasis on home automation and media services
6. Streamlined variable organization
7. Reduced complexity in deployment paths
