# Proxmox Role

A comprehensive Ansible role for managing Proxmox VE servers, including system configuration, VM management, and resource optimization.

## Features

- System Configuration (CPU, Memory, Storage)
- Network Management
- Security Hardening
- Container (LXC) Management
- Virtual Machine Management
- Resource Management
- Monitoring and Maintenance
- Backup Management
- Cluster Configuration
- Email Notifications

## VM Resource Management

Manage VM resources including CPU, memory, limits, and monitoring options.

### CPU Configuration

```yaml
proxmox_vm:
  cpu:
    - vmid: 100      # VM ID
      cores: 2       # Number of CPU cores
      sockets: 1     # Number of CPU sockets
      vcpus: 2       # Total number of vCPUs (cores * sockets)
      type: "host"   # CPU type (host, kvm64, etc.)
      numa: false    # NUMA enabled/disabled
```

### Memory Configuration

```yaml
proxmox_vm:
  memory:
    - vmid: 100
      size: 2048     # Memory size in MB
      balloon: 1024  # Minimum memory size with ballooning
      shares: 2000   # Memory shares for ballooning
      hugepages: "2MB" # Hugepages size
      swap: 512      # Swap size in MB
```

### Resource Limits

```yaml
proxmox_vm:
  limits:
    - vmid: 100
      cpu: 2         # CPU limit (0-N)
      cpuunits: 2048 # CPU units for fair CPU scheduling
      io_thread: true # Enable/disable IO thread
```

### Monitoring Configuration

```yaml
proxmox_vm:
  monitoring:
    - vmid: 100
      agent: true    # Enable/disable QEMU guest agent
      freeze: false  # Enable/disable freeze on suspend
```

### Available Tags

- `vm`: All VM-related tasks
- `resources`: Resource management tasks
- `cpu`: CPU configuration tasks
- `memory`: Memory configuration tasks
- `limits`: Resource limit tasks
- `monitoring`: Monitoring configuration tasks

## Usage

Include the role in your playbook with appropriate variables:

```yaml
- hosts: proxmox
  roles:
    - role: proxmox
      vars:
        proxmox_vm:
          cpu:
            - vmid: 100
              cores: 2
              sockets: 1
```

## Requirements

- Ansible 2.9 or higher
- Proxmox VE 7.0 or higher
- Python 3.x on managed nodes
- `proxmoxer` Python package
- `requests` Python package

## License

MIT

## Author Information

Created and maintained by your organization/team.

## Documentation

Detailed documentation is available for specific components:

- [LXC Networking Configuration](docs/lxc-networking.md)
- [Network Configuration](docs/network.md)
- [Storage Configuration](docs/storage.md)
- [Repository Management](docs/repository.md)
- [Security Configuration](docs/security.md)
- [VM Configuration](docs/vm.md) 
