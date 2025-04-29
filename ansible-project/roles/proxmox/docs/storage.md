# Proxmox Storage Configuration

This document describes the storage configuration options available in the Proxmox role.

## Overview

The storage configuration manages local and shared storage, including ZFS pools, LVM volumes, directories, and network storage.

## Configuration Options

### Basic Storage Settings

```yaml
proxmox_storage:
  default_storage: "local-lvm"
  enable_zfs: true
  enable_ceph: false
  storage_threshold: 85
```

- `default_storage`: Default storage for new VMs/containers
- `enable_zfs`: Enable ZFS support
- `enable_ceph`: Enable Ceph support
- `storage_threshold`: Storage usage threshold (%)

### Local Storage Configuration

```yaml
local_storage:
  - name: "local-lvm"
    type: "lvmthin"
    vgname: "pve"
    thinpool: "data"
    content: ["images", "rootdir"]
    shared: false
    maxfiles: 0
  - name: "local"
    type: "dir"
    path: "/var/lib/proxmox"
    content: ["backup", "iso", "snippets"]
    maxfiles: 0
```

- `name`: Storage name
- `type`: Storage type
- `path`: Storage path
- `content`: Allowed content types
- `shared`: Shared storage flag
- `maxfiles`: Maximum file limit

### ZFS Configuration

```yaml
zfs_pools:
  - name: "tank"
    devices: ["/dev/sdb", "/dev/sdc"]
    raidz: 1
    compression: "lz4"
    atime: "off"
    relatime: "on"
    mountpoint: "/tank"
```

- `name`: Pool name
- `devices`: Device list
- `raidz`: RAID level
- `compression`: Compression algorithm
- `atime`: Access time updates
- `relatime`: Relative access time
- `mountpoint`: Mount point

### Network Storage Configuration

```yaml
network_storage:
  nfs:
    - name: "nfs-storage"
      server: "nas.example.com"
      export: "/exports/proxmox"
      content: ["images", "iso"]
      options: "vers=4,soft"
  cifs:
    - name: "cifs-backup"
      server: "backup.example.com"
      share: "backups"
      username: "backup-user"
      password: "secure-password"
      content: ["backup"]
```

- `name`: Storage name
- `server`: Storage server
- `export/share`: Export/share path
- `content`: Allowed content
- `options`: Mount options

### Storage Features

```yaml
storage_features:
  backup:
    compression: "zstd"
    schedule: "0 2 * * *"
    retention: 7
  replication:
    enabled: true
    target: "backup.example.com"
    schedule: "0 3 * * *"
```

- `backup`: Backup settings
- `replication`: Replication settings
- `compression`: Compression settings
- `schedule`: Task schedules

## Usage Examples

### Basic Storage Setup

```yaml
proxmox_storage:
  default_storage: "local-lvm"
  local_storage:
    - name: "local-lvm"
      type: "lvmthin"
      vgname: "pve"
      thinpool: "data"
```

### Advanced Storage Configuration

```yaml
proxmox_storage:
  enable_zfs: true
  zfs_pools:
    - name: "tank"
      devices: ["/dev/sdb", "/dev/sdc"]
      raidz: 1
  network_storage:
    nfs:
      - name: "shared-storage"
        server: "nas.example.com"
        export: "/exports/proxmox"
```

## Best Practices

1. **Storage Planning**
   - Size appropriately
   - Consider I/O patterns
   - Plan for growth
   - Regular monitoring

2. **Performance Optimization**
   - Use appropriate storage types
   - Optimize mount options
   - Monitor I/O patterns
   - Regular maintenance

3. **Data Protection**
   - Regular backups
   - Snapshot management
   - Replication setup
   - Monitoring and alerts

4. **Security**
   - Access control
   - Network isolation
   - Encryption setup
   - Regular audits

## Troubleshooting

Common issues and their solutions:

1. **Storage Performance**
   - Check I/O stats
   - Monitor latency
   - Review cache usage
   - Optimize settings

2. **Space Issues**
   - Monitor usage
   - Clean old data
   - Expand storage
   - Optimize allocation

3. **Network Storage**
   - Check connectivity
   - Verify permissions
   - Monitor latency
   - Test throughput

4. **ZFS Problems**
   - Check pool status
   - Verify scrub results
   - Monitor memory usage
   - Review logs

## Storage Monitoring

### Performance Monitoring

```yaml
monitoring:
  enabled: true
  storages: ["local-lvm", "tank"]
  metrics:
    - usage
    - iops
    - latency
    - throughput
  interval: 300
```

### Storage Health Checks

```yaml
health_checks:
  enabled: true
  checks:
    - space_usage
    - inode_usage
    - smart_status
    - zfs_status
  interval: "6h"
```

## Security Considerations

1. **Access Control**
   - Restrict permissions
   - Use secure protocols
   - Monitor access
   - Regular audits

2. **Data Protection**
   - Enable encryption
   - Secure backups
   - Monitor changes
   - Regular verification

3. **Network Security**
   - Isolate storage network
   - Use secure protocols
   - Monitor traffic
   - Regular security updates 
