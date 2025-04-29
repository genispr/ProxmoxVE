# Proxmox Backup Configuration

This document describes the backup configuration options available in the Proxmox role, including both standard Proxmox VE backups and Proxmox Backup Server (PBS) integration.

## Overview

The backup configuration manages all aspects of backup operations, including scheduling, retention, performance, and PBS integration.

## Configuration Options

### Basic Backup Settings

```yaml
proxmox_backup:
  enabled: false
  storage: "local"
  backup_dir: "/var/lib/vz/dump"
  restore_dir: "/var/lib/vz/restore"
  mode: "snapshot"  # snapshot, suspend, or stop
  compress: "zstd"  # Compression algorithm
```

- `enabled`: Enable/disable backup functionality
- `storage`: Storage location for backups
- `backup_dir`: Directory for backup files
- `restore_dir`: Directory for restore operations
- `mode`: Backup mode (snapshot, suspend, or stop)
- `compress`: Compression algorithm to use

### Backup Schedule

```yaml
schedule:
  minute: "0"
  hour: "0"
  day: "*"
  month: "*"
  weekday: "*"
```

- Standard cron format for scheduling backups
- Use `*` for any value
- Supports ranges (e.g., `1-5`) and lists (e.g., `1,3,5`)

### Retention Settings

```yaml
retention:
  keep_last: 7     # Number of backups to keep
  keep_hourly: 24  # Hourly backups to keep
  keep_daily: 7    # Daily backups to keep
  keep_weekly: 4   # Weekly backups to keep
  keep_monthly: 2  # Monthly backups to keep
```

- `keep_last`: Most recent backups to retain
- `keep_hourly`: Hourly backup retention
- `keep_daily`: Daily backup retention
- `keep_weekly`: Weekly backup retention
- `keep_monthly`: Monthly backup retention

### Performance Settings

```yaml
performance:
  bandwidth_limit: 0  # 0 means unlimited
  ionice: 5          # IO priority (0-7)
  max_workers: 1      # Maximum parallel backup jobs
```

- `bandwidth_limit`: Network bandwidth limit in MB/s
- `ionice`: IO priority for backup operations
- `max_workers`: Maximum concurrent backup jobs

### PBS Configuration

```yaml
pbs:
  enabled: false
  remove_subscription_nag: true
  web_interface_port: 8007
  
  storage:
    path: "/var/lib/proxmox-backup"
    gc_schedule: "daily"
    prune_schedule: "daily"
    
  authentication:
    realm: "pbs"
    token_lifetime: 28800
```

- `enabled`: Enable PBS integration
- `remove_subscription_nag`: Remove subscription warning
- `web_interface_port`: Web UI port
- `storage`: PBS storage configuration
- `authentication`: PBS authentication settings

## Usage Examples

### Basic VM Backup

```yaml
proxmox_backup:
  enabled: true
  storage: "local"
  mode: "snapshot"
  schedule:
    hour: "2"
    minute: "0"
  retention:
    keep_daily: 7
    keep_weekly: 4
```

### PBS Integration

```yaml
proxmox_backup:
  enabled: true
  pbs:
    enabled: true
    storage:
      path: "/mnt/pbs_storage"
    authentication:
      realm: "pbs"
      token_lifetime: 28800
```

## Best Practices

1. **Scheduling**
   - Schedule backups during off-peak hours
   - Stagger backup times for different VMs/CTs
   - Consider backup window duration

2. **Storage Management**
   - Monitor storage space regularly
   - Implement appropriate retention policies
   - Use compression for storage efficiency

3. **Performance Optimization**
   - Set appropriate bandwidth limits
   - Configure IO priority based on system load
   - Limit concurrent backup jobs

4. **PBS Integration**
   - Use secure authentication
   - Configure appropriate token lifetimes
   - Regular garbage collection and pruning

## Monitoring and Notifications

### Email Notifications

```yaml
notifications:
  enabled: false
  on_success: false
  on_failure: true
  mailto: ""
```

### PBS Monitoring

```yaml
monitoring:
  enabled: true
  interval: 300
  metrics:
    - backup_status
    - storage_usage
    - task_status
```

## Troubleshooting

Common issues and their solutions:

1. **Backup Failures**
   - Check storage space
   - Verify backup mode compatibility
   - Review backup logs

2. **Performance Issues**
   - Adjust bandwidth limits
   - Check IO priorities
   - Monitor system resources

3. **PBS Integration Problems**
   - Verify network connectivity
   - Check authentication settings
   - Review PBS logs

4. **Storage Issues**
   - Monitor storage usage
   - Check retention policy effectiveness
   - Verify compression settings 
