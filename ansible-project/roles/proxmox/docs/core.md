# Proxmox Core System Configuration

This document describes the core system configuration options available in the Proxmox role.

## Overview

The core system configuration manages basic system settings, performance tuning, storage optimization, and system logging.

## Configuration Options

### Basic System Configuration

```yaml
proxmox_hostname: ""
proxmox_timezone: "UTC"
proxmox_locales: ["en_US.UTF-8"]
proxmox_default_locale: "en_US.UTF-8"
proxmox_keyboard_layout: "us"
```

- `proxmox_hostname`: System hostname
- `proxmox_timezone`: System timezone
- `proxmox_locales`: List of enabled locales
- `proxmox_default_locale`: Default system locale
- `proxmox_keyboard_layout`: Keyboard layout

### System Performance Configuration

```yaml
proxmox_sysctl:
  # VM/CT Memory Management
  vm_swappiness: 10
  vm_overcommit_memory: 1
  vm_min_free_kbytes: 262144
  vm_dirty_ratio: 20
  vm_dirty_background_ratio: 10

  # Network Performance
  net_core_somaxconn: 1024
  net_core_netdev_max_backlog: 5000
  net_ipv4_tcp_max_syn_backlog: 4096
  net_ipv4_tcp_fastopen: 3
  net_ipv4_tcp_tw_reuse: 1

  # File System & I/O
  fs_file_max: 1048576
  fs_inotify_max_user_watches: 524288
  fs_aio_max_nr: 1048576
```

#### Memory Management
- `vm_swappiness`: How aggressively swap memory is used (0-100)
- `vm_overcommit_memory`: Memory overcommit policy
- `vm_min_free_kbytes`: Minimum free memory
- `vm_dirty_ratio`: Maximum memory for dirty pages
- `vm_dirty_background_ratio`: Background writeback threshold

#### Network Performance
- `net_core_somaxconn`: Maximum queue length
- `net_core_netdev_max_backlog`: Network interface backlog
- `net_ipv4_tcp_max_syn_backlog`: TCP SYN backlog
- `net_ipv4_tcp_fastopen`: TCP Fast Open configuration
- `net_ipv4_tcp_tw_reuse`: TCP time-wait reuse

#### File System
- `fs_file_max`: Maximum number of file handles
- `fs_inotify_max_user_watches`: Maximum inotify watches
- `fs_aio_max_nr`: Maximum async I/O requests

### Storage Performance

```yaml
proxmox_storage_optimizations:
  zfs_arc_max: "{{ (ansible_memtotal_mb * 0.5) | int }}M"
  zfs_options:
    compression: "lz4"
    atime: "off"
    xattr: "sa"
  ext4_options: "noatime,nodiratime,discard"
```

- `zfs_arc_max`: Maximum ZFS ARC size
- `zfs_options`: ZFS-specific optimizations
- `ext4_options`: EXT4 mount options

### System Logging

```yaml
proxmox_logging:
  ram_enabled: true
  ram_size: "128M"
  ram_compress: true
  ram_mount: "/var/log.ram"
  backup_enabled: true
  backup_interval: "1h"
  backup_retention: 7
  paths:
    - "/var/log"
    - "/var/log/journal"
    - "/var/log/nginx"
    - "/var/log/containers"
    - "/var/log/pods"
```

- `ram_enabled`: Enable RAM-based logging
- `ram_size`: Size of RAM disk for logs
- `ram_compress`: Enable log compression
- `backup_enabled`: Enable log backups
- `backup_interval`: Backup frequency
- `backup_retention`: Days to keep backups
- `paths`: Log paths to manage

## Usage Examples

### Basic System Setup

```yaml
proxmox_hostname: "pve1"
proxmox_timezone: "Europe/London"
proxmox_locales: ["en_GB.UTF-8"]
proxmox_default_locale: "en_GB.UTF-8"
proxmox_keyboard_layout: "uk"
```

### Performance-Optimized Configuration

```yaml
proxmox_sysctl:
  vm_swappiness: 5
  vm_overcommit_memory: 1
  net_core_somaxconn: 2048
  fs_file_max: 2097152

proxmox_storage_optimizations:
  zfs_arc_max: "16G"
  zfs_options:
    compression: "lz4"
    atime: "off"
```

## Best Practices

1. **System Settings**
   - Set appropriate timezone and locale
   - Configure keyboard layout for console access
   - Use meaningful hostnames

2. **Memory Management**
   - Adjust swappiness based on workload
   - Configure appropriate memory overcommit
   - Monitor memory usage patterns

3. **Network Optimization**
   - Tune network parameters for workload
   - Monitor network performance
   - Adjust backlog queues appropriately

4. **Storage Configuration**
   - Use appropriate filesystem options
   - Configure ZFS ARC size based on available memory
   - Enable compression where beneficial

5. **Logging**
   - Use RAM-based logging for high-write environments
   - Configure appropriate backup retention
   - Monitor log storage usage

## Troubleshooting

Common issues and their solutions:

1. **Performance Issues**
   - Check sysctl parameters
   - Monitor system resources
   - Review storage performance

2. **Memory Problems**
   - Adjust swappiness
   - Check memory overcommit settings
   - Monitor swap usage

3. **Storage Issues**
   - Verify ZFS ARC size
   - Check filesystem mount options
   - Monitor I/O performance

4. **Logging Problems**
   - Check RAM disk size
   - Verify backup configuration
   - Monitor log rotation

## Security Considerations

1. **System Access**
   - Configure secure hostname resolution
   - Use appropriate file permissions
   - Monitor system access

2. **Resource Limits**
   - Set appropriate file limits
   - Configure memory limits
   - Monitor resource usage

3. **Logging Security**
   - Secure log storage
   - Configure log retention
   - Monitor log access 
