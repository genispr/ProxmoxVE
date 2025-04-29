# Proxmox VM Configuration

This document describes the virtual machine configuration options available in the Proxmox role.

## Overview

The VM configuration manages virtual machine resources, devices, networking, storage, and monitoring settings.

## Configuration Options

### Basic VM Settings

```yaml
proxmox_vm:
  defaults:
    vmid_start: 100        # Starting VM ID
    template: "debian-11"  # Default template
    node: "proxmox"        # Default node
    onboot: true           # Start VM at boot time
    protection: false      # Protect from deletion
    startup_order: 1       # VM startup order
    startup_delay: 60      # Delay between startups (seconds)
```

- `vmid_start`: Starting VM ID for new VMs
- `template`: Default template to use for new VMs
- `node`: Default node to place VMs
- `onboot`: Start VM at system boot
- `protection`: Protect VM from accidental deletion
- `startup_order`: VM startup order (lower = earlier)
- `startup_delay`: Delay between VM startups in seconds

### CPU Configuration

```yaml
cpu:
  - vmid: 100      # VM ID
    cores: 2       # Number of CPU cores
    sockets: 1     # Number of CPU sockets
    vcpus: 2       # Total number of vCPUs (cores * sockets)
    type: "host"   # CPU type (host, kvm64, etc.)
    numa: false    # NUMA enabled/disabled
    flags:
      - "+aes"     # CPU flags to enable
      - "-mtrr"    # CPU flags to disable
```

- `vmid`: Virtual machine ID
- `cores`: Number of CPU cores per socket
- `sockets`: Number of CPU sockets
- `vcpus`: Total number of vCPUs
- `type`: CPU type (host, kvm64, x86-64-v2, etc.)
- `numa`: NUMA enabled/disabled
- `flags`: CPU flags to enable/disable

### Memory Configuration

```yaml
memory:
  - vmid: 100
    size: 2048     # Memory size in MB
    balloon: 1024  # Minimum memory size with ballooning
    shares: 2000   # Memory shares for ballooning
    hugepages: "2MB" # Hugepages size
    swap: 512      # Swap size in MB
    locked: false  # Lock memory (disable swapping to disk)
```

- `size`: Memory size in MB
- `balloon`: Minimum memory size when ballooning is active
- `shares`: Memory shares for ballooning algorithm
- `hugepages`: Hugepages size (2MB, 1GB, etc.)
- `swap`: Swap size in MB
- `locked`: Lock memory to prevent swapping to disk

### Resource Limits

```yaml
limits:
  - vmid: 100
    cpu: 2         # CPU limit (0-N)
    cpuunits: 2048 # CPU units for fair CPU scheduling
    io_thread: true # Enable/disable IO thread
    io_uring: true # Enable/disable IO_uring
    memory_min: 512 # Minimum memory reservation (MB)
    memory_max: 4096 # Maximum memory allocation (MB)
```

- `cpu`: CPU usage limit (e.g., 2 = 200% of one core)
- `cpuunits`: CPU units for fair CPU scheduling
- `io_thread`: Enable/disable IO thread
- `io_uring`: Enable/disable IO_uring for improved IO performance
- `memory_min`: Minimum memory reservation in MB
- `memory_max`: Maximum memory allocation in MB

### Network Configuration

```yaml
network:
  - vmid: 100
    interfaces:
      - id: "net0"
        model: "virtio"
        bridge: "vmbr0"
        mac: "auto"
        firewall: true
        vlan: 10
        rate: 100
        mtu: 1500
```

- `id`: Network interface ID (net0, net1, etc.)
- `model`: Network device model (virtio, e1000, etc.)
- `bridge`: Bridge to connect to
- `mac`: MAC address or "auto" for automatic assignment
- `firewall`: Enable/disable firewall for this interface
- `vlan`: VLAN tag
- `rate`: Rate limit in Mbps
- `mtu`: Maximum Transmission Unit

### Storage Configuration

```yaml
storage:
  - vmid: 100
    disks:
      - id: "scsi0"
        storage: "local-lvm"
        size: "20G"
        format: "raw"
        cache: "writeback"
        ssd: true
        discard: true
        backup: true
        iothread: true
```

- `id`: Disk ID (scsi0, virtio0, etc.)
- `storage`: Storage pool to use
- `size`: Disk size
- `format`: Disk format (raw, qcow2, etc.)
- `cache`: Cache mode (none, writeback, writethrough)
- `ssd`: Enable SSD emulation
- `discard`: Enable discard/TRIM
- `backup`: Include disk in backups
- `iothread`: Use IO thread for disk operations

### Monitoring Configuration

```yaml
monitoring:
  - vmid: 100
    agent: true    # Enable/disable QEMU guest agent
    freeze: false  # Enable/disable freeze on suspend
    metrics:
      - cpu
      - memory
      - network
      - disk
    alerts:
      cpu_usage:
        threshold: 90
        duration: "5m"
      memory_usage:
        threshold: 95
        duration: "10m"
```

- `agent`: Enable/disable QEMU guest agent
- `freeze`: Enable/disable freeze on suspend
- `metrics`: Metrics to collect
- `alerts`: Alert configuration and thresholds

## Usage Examples

### Basic VM Setup

```yaml
proxmox_vm:
  defaults:
    vmid_start: 100
    template: "debian-11"
    node: "proxmox"
  cpu:
    - vmid: 100
      cores: 2
      sockets: 1
  memory:
    - vmid: 100
      size: 2048
```

### High-Performance VM Configuration

```yaml
proxmox_vm:
  cpu:
    - vmid: 101
      cores: 4
      sockets: 2
      type: "host"
      flags:
        - "+aes"
        - "+avx"
  memory:
    - vmid: 101
      size: 8192
      hugepages: "1GB"
  storage:
    - vmid: 101
      disks:
        - id: "scsi0"
          storage: "nvme-pool"
          size: "50G"
          ssd: true
          discard: true
          iothread: true
```

## Best Practices

1. **VM Planning**
   - Size appropriately
   - Consider resource overcommitment
   - Plan for growth
   - Regular monitoring

2. **Performance Optimization**
   - Use virtio devices
   - Enable IO threads
   - Configure hugepages
   - Tune CPU/memory settings

3. **Resource Management**
   - Use ballooning
   - Set appropriate limits
   - Monitor resource usage
   - Use NUMA when available

4. **Security**
   - Isolate VM networks
   - Enable firewall
   - Regular updates
   - Monitoring for anomalies

## Troubleshooting

Common issues and their solutions:

1. **Performance Issues**
   - Check CPU limits
   - Monitor memory usage
   - Review disk I/O
   - Check network bottlenecks

2. **Boot Problems**
   - Verify boot order
   - Check storage availability
   - Review VM logs
   - Validate configuration

3. **Device Issues**
   - Verify device compatibility
   - Check driver support
   - Review device passthrough
   - Update guest agents

## VM Monitoring

### Performance Monitoring

```yaml
monitoring:
  enabled: true
  vms: [100, 101, 102]
  metrics:
    - cpu_usage
    - memory_usage
    - disk_iops
    - network_traffic
  interval: 30
```

### VM Health Checks

```yaml
health_checks:
  enabled: true
  checks:
    - vm_status
    - agent_status
    - resource_usage
    - snapshot_status
  interval: "1h"
```

## Security Considerations

1. **VM Isolation**
   - Network segmentation
   - Resource limits
   - Storage isolation
   - Security policies

2. **Secure Boot**
   - TPM support
   - UEFI secure boot
   - Boot verification
   - Encryption options 
