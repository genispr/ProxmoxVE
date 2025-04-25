# Proxmox LXC Manager Role

This role manages LXC containers on Proxmox VE hosts, including creation, configuration, and maintenance.

## Requirements

- Proxmox VE 7.x or higher
- Root access to Proxmox host
- Valid LXC template available in Proxmox storage

## Role Variables

### Required Variables

```yaml
container_id: "100"                    # Unique ID for the container
container_hostname: "container1"        # Hostname for the container
container_template: "local:vztmpl/debian-12-standard_12.2-1_amd64.tar.zst"
```

### Optional Variables

```yaml
# Resource configuration
container_cores: 2                     # Number of CPU cores
container_memory: 2048                 # Memory in MB
container_swap: 512                    # Swap in MB
container_disk: "8G"                   # Disk size

# Network configuration
container_network:
  net0: "name=eth0,bridge=vmbr0,ip=dhcp"
  net1: "name=eth1,bridge=vmbr1,ip=none"

# Feature configuration
container_features:
  nesting: false                       # Enable nesting
  keyctl: true                        # Enable key control
  fuse: false                         # Enable FUSE
  mount: nfs                          # Allow NFS mounts
  dri: false                         # Enable hardware acceleration

# Storage configuration
container_rootfs: "local-lvm"         # Storage for root filesystem
container_mp:                         # Additional mount points
  mp0: "local-lvm:100,mp=/mnt/data"

# System configuration
container_timezone: "Europe/Madrid"    # Timezone (Barcelona, Spain)
container_dns:
  - "1.1.1.1"
  - "1.0.0.1"
container_searchdomain: "local"
```

## Dependencies

- Role: pve_post_install (for initial Proxmox setup)

## Example Playbook

```yaml
- hosts: proxmox
  roles:
    - role: proxmox_lxc_manager
      vars:
        container_id: "101"
        container_hostname: "docker01"
        container_template: "local:vztmpl/debian-12-standard_12.2-1_amd64.tar.zst"
        container_cores: 4
        container_memory: 4096
        container_features:
          nesting: true
          keyctl: true
        container_network:
          net0: "name=eth0,bridge=vmbr0,ip=dhcp"
```

## Role Structure

```
proxmox_lxc_manager/
├── defaults/
│   └── main.yml           # Default variables
├── handlers/
│   └── main.yml           # Handlers for container operations
├── tasks/
│   ├── main.yml          # Main tasks file
│   ├── preflight.yml     # Preflight checks
│   ├── create.yml        # Container creation
│   ├── network.yml       # Network configuration
│   └── configure.yml     # Post-creation configuration
├── templates/
│   └── container_config.j2  # Container configuration template
└── README.md            # This documentation
```

## Tags

- `lxc_create`: Container creation tasks
- `lxc_network`: Network configuration tasks
- `lxc_config`: Container configuration tasks
- `lxc_all`: All container tasks

## License

MIT 
