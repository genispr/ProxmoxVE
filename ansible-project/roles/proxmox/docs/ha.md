# Proxmox High Availability Configuration

This document describes the high availability (HA) configuration options available in the Proxmox role.

## Overview

The HA configuration manages resource availability, failover behavior, and service monitoring across the Proxmox cluster.

## Configuration Options

### Basic HA Settings

```yaml
proxmox_ha:
  enabled: false
```

- `enabled`: Enable/disable HA functionality

### Resource Configuration

```yaml
resources:
  defaults:
    state: "started"
    max_restart: 1
    max_relocate: 1
  
  groups:
    - name: "default"
      restricted: false
      nodes: []
      
  types:
    vm:
      enabled: true
      startup_delay: 0
      shutdown_timeout: 180
    ct:
      enabled: true
      startup_delay: 0
      shutdown_timeout: 180
```

- `defaults`: Default settings for all HA resources
- `groups`: Resource group definitions
- `types`: Configuration for different resource types (VMs and CTs)

### Fencing Configuration

```yaml
fencing:
  enabled: true
  timeout: 60
  delay: 5
```

- `enabled`: Enable/disable fencing
- `timeout`: Fencing operation timeout
- `delay`: Delay before fencing operations

### Failover Configuration

```yaml
failover:
  auto_failback: true
  failback_delay: 5
  migration_timeout: 300
```

- `auto_failback`: Automatically fail back resources
- `failback_delay`: Delay before failback
- `migration_timeout`: Maximum time for migration

### Network Configuration

```yaml
network:
  allowed_networks: []
  deny_networks: []
  dedicated_network: false
  interface: "eth0"
```

- `allowed_networks`: Networks allowed for HA communication
- `deny_networks`: Networks denied for HA communication
- `dedicated_network`: Use dedicated network for HA
- `interface`: Network interface for HA communication

### Service Configuration

```yaml
services:
  pveproxy:
    enabled: true
    priority: 100
  pvedaemon:
    enabled: true
    priority: 100
  pve-cluster:
    enabled: true
    priority: 90
  corosync:
    enabled: true
    priority: 90
```

- Service-specific HA settings
- Priority levels for service recovery

## Usage Examples

### Basic HA Setup

```yaml
proxmox_ha:
  enabled: true
  resources:
    groups:
      - name: "production"
        nodes: ["node1", "node2"]
    types:
      vm:
        enabled: true
        shutdown_timeout: 300
```

### Advanced HA Configuration

```yaml
proxmox_ha:
  enabled: true
  failover:
    auto_failback: true
    migration_timeout: 600
  fencing:
    enabled: true
    timeout: 120
  network:
    dedicated_network: true
    interface: "bond0"
```

## Best Practices

1. **Resource Management**
   - Group related resources together
   - Set appropriate restart and relocate limits
   - Configure realistic timeouts

2. **Network Configuration**
   - Use dedicated network for HA traffic
   - Configure appropriate network restrictions
   - Monitor network latency

3. **Fencing Configuration**
   - Enable fencing for reliable failover
   - Set appropriate timeouts
   - Test fencing configuration

4. **Service Priorities**
   - Configure service priorities based on dependencies
   - Set appropriate startup delays
   - Monitor service health

## Advanced Settings

### Maintenance Mode

```yaml
maintenance:
  quorum_policy: "freeze"  # freeze, ignore, or stop
  relocate_policy: "ratio" # ratio or utilization
```

### Recovery Configuration

```yaml
recovery:
  max_attempts: 3
  retry_interval: 300
  auto_restart: true
```

### Monitoring Configuration

```yaml
monitoring:
  enabled: true
  interval: 60
  watchdog:
    enabled: true
    device: "/dev/watchdog"
    timeout: 60
```

## Troubleshooting

Common issues and their solutions:

1. **Resource Failover Issues**
   - Check network connectivity
   - Verify fencing configuration
   - Review migration settings

2. **Service Recovery Problems**
   - Check service priorities
   - Verify service dependencies
   - Monitor service logs

3. **Network Issues**
   - Test network connectivity
   - Check network interface configuration
   - Monitor network latency

4. **Quorum Problems**
   - Verify node count
   - Check network connectivity
   - Review quorum policy settings

## Migration Strategies

1. **Live Migration**
   - Use for minimal downtime
   - Monitor network bandwidth
   - Set appropriate timeouts

2. **Cold Migration**
   - Use for resource-intensive VMs
   - Plan maintenance windows
   - Consider storage migration time

## Security Considerations

1. **Network Security**
   - Restrict HA traffic to trusted networks
   - Use network encryption where possible
   - Monitor for unauthorized access

2. **Resource Access**
   - Configure appropriate resource restrictions
   - Use group-based access control
   - Monitor resource usage 
