# Proxmox Monitoring Configuration

This document describes the monitoring configuration options available in the Proxmox role.

## Overview

The monitoring configuration manages system metrics collection, service monitoring, resource tracking, and performance analysis across the Proxmox environment.

## Configuration Options

### Basic Monitoring Settings

```yaml
proxmox_monitoring:
  enabled: true
  collectd: true
  
  # System Metrics
  system:
    enabled: true
    interval: 60
    retention: "30d"
    metrics:
      - cpu
      - memory
      - disk
      - network
      - process
      - system
```

- `enabled`: Enable/disable monitoring functionality
- `collectd`: Enable collectd integration
- `system`: Basic system metrics configuration

### Service Monitoring

```yaml
services:
  enabled: true
  interval: 300
  check_list:
    - pveproxy
    - pvedaemon
    - pve-cluster
    - corosync
    - sshd
    - nginx
```

- `enabled`: Enable service monitoring
- `interval`: Check interval in seconds
- `check_list`: List of services to monitor

### Resource Monitoring

```yaml
resources:
  enabled: true
  interval: 60
  thresholds:
    cpu_usage: 80
    memory_usage: 85
    disk_usage: 90
    inode_usage: 90
```

- `enabled`: Enable resource monitoring
- `interval`: Check interval in seconds
- `thresholds`: Resource usage thresholds

### Performance Monitoring

```yaml
performance:
  enabled: true
  interval: 300
  metrics:
    - io_stats
    - tcp_stats
    - vm_stats
    - zfs_stats
```

- `enabled`: Enable performance monitoring
- `interval`: Check interval in seconds
- `metrics`: Performance metrics to collect

### SMART Monitoring

```yaml
smart:
  enabled: true
  schedule: "weekly"
  attributes:
    - reallocated_sector_ct
    - spin_retry_count
    - command_timeout
    - current_pending_sector
    - offline_uncorrectable
    - raw_read_error_rate
```

- `enabled`: Enable SMART monitoring
- `schedule`: Check frequency
- `attributes`: SMART attributes to monitor

### ZFS Monitoring

```yaml
zfs:
  enabled: true
  scrub_schedule: "monthly"
  snapshot_schedule: "daily"
  metrics:
    - pool_health
    - pool_capacity
    - arc_stats
    - io_stats
```

- `enabled`: Enable ZFS monitoring
- `scrub_schedule`: ZFS scrub frequency
- `snapshot_schedule`: Snapshot frequency
- `metrics`: ZFS metrics to monitor

### Health Checks

```yaml
health_check:
  enabled: true
  interval: "6h"
  checks:
    - disk_health
    - raid_health
    - service_health
    - network_health
    - cluster_health
    - backup_health
```

- `enabled`: Enable health checks
- `interval`: Check interval
- `checks`: Health checks to perform

## Usage Examples

### Basic Monitoring Setup

```yaml
proxmox_monitoring:
  enabled: true
  system:
    enabled: true
    interval: 60
    metrics:
      - cpu
      - memory
      - disk
  services:
    enabled: true
    check_list:
      - pveproxy
      - pvedaemon
```

### Comprehensive Monitoring

```yaml
proxmox_monitoring:
  enabled: true
  system:
    enabled: true
    retention: "90d"
  performance:
    enabled: true
    interval: 60
  smart:
    enabled: true
  zfs:
    enabled: true
    scrub_schedule: "weekly"
```

## Best Practices

1. **Metric Collection**
   - Choose appropriate monitoring intervals
   - Balance monitoring detail with system impact
   - Configure suitable retention periods

2. **Resource Monitoring**
   - Set realistic thresholds
   - Configure appropriate alerts
   - Monitor trends over time

3. **Storage Monitoring**
   - Enable SMART monitoring for physical disks
   - Configure regular ZFS scrubs
   - Monitor storage performance metrics

4. **Service Monitoring**
   - Monitor critical services
   - Set appropriate check intervals
   - Configure service dependencies

## Alert Configuration

```yaml
alerts:
  enabled: true
  handlers:
    - email
    - webhook
  rules:
    high_cpu:
      threshold: 90
      duration: "5m"
      severity: warning
    high_memory:
      threshold: 85
      duration: "5m"
      severity: warning
    disk_full:
      threshold: 90
      duration: "5m"
      severity: critical
    service_down:
      duration: "5m"
      severity: critical
```

## Troubleshooting

Common issues and their solutions:

1. **Metric Collection Issues**
   - Check collector service status
   - Verify storage capacity
   - Review collection intervals

2. **Alert Problems**
   - Verify alert configurations
   - Check notification channels
   - Review alert thresholds

3. **Performance Impact**
   - Adjust collection intervals
   - Review metric retention
   - Monitor collector resource usage

4. **Storage Monitoring**
   - Check SMART monitoring compatibility
   - Verify ZFS pool status
   - Review storage metrics

## Security Considerations

1. **Data Protection**
   - Secure metric storage
   - Configure appropriate retention
   - Protect monitoring endpoints

2. **Access Control**
   - Restrict monitoring access
   - Secure alert channels
   - Monitor access patterns

3. **Network Security**
   - Secure monitoring traffic
   - Configure appropriate firewalls
   - Monitor suspicious activity 
