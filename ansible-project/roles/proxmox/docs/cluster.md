# Proxmox Cluster Configuration

This document describes the cluster configuration options available in the Proxmox role.

## Overview

The cluster configuration manages Proxmox cluster setup, including node management, network configuration, and resource distribution.

## Configuration Options

### Basic Cluster Settings

```yaml
proxmox_cluster:
  enabled: false
  name: "proxmox-cluster"
```

- `enabled`: Enable/disable cluster functionality
- `name`: Name of the cluster (must be unique)

### Node Configuration

```yaml
nodes: []  # List of cluster nodes
quorum_votes: 1
link_mode: "static"  # static or dynamic
```

- `nodes`: List of nodes to include in the cluster
- `quorum_votes`: Number of votes required for quorum
- `link_mode`: Network link mode (static or dynamic)

### Network Configuration

```yaml
network:
  allowed_networks: []  # Networks allowed for cluster communication
  deny_networks: []     # Networks denied for cluster communication
  link_count: 1        # Number of network links between nodes
```

- `allowed_networks`: List of networks allowed for cluster communication
- `deny_networks`: List of networks to deny for cluster communication
- `link_count`: Number of network links between nodes

### Corosync Configuration

```yaml
corosync:
  token: 2000
  token_retransmits: 4
  secauth: true
  interface: "eth0"
  redundant_ring: false
```

- `token`: Token timeout in milliseconds
- `token_retransmits`: Number of token retransmissions
- `secauth`: Enable/disable secure authentication
- `interface`: Network interface for corosync
- `redundant_ring`: Enable second ring for redundancy

### Join/Leave Settings

```yaml
join:
  timeout: 180
  force: false
```

- `timeout`: Seconds to wait for join operations
- `force`: Force join even if node was part of another cluster

### Resource Distribution

```yaml
resource_distribution:
  auto_failback: true
  migration_timeout: 300
  max_parallel_migrations: 1
```

- `auto_failback`: Automatically fail back resources
- `migration_timeout`: Maximum time for migration operations
- `max_parallel_migrations`: Maximum number of parallel migrations

### Monitoring

```yaml
monitoring:
  enabled: true
  interval: 60
  fence_timeout: 180
```

- `enabled`: Enable cluster monitoring
- `interval`: Monitoring check interval in seconds
- `fence_timeout`: Timeout for fencing operations

## Usage Examples

### Basic Cluster Setup

```yaml
proxmox_cluster:
  enabled: true
  name: "prod-cluster"
  nodes:
    - name: "pve1"
      id: 1
    - name: "pve2"
      id: 2
```

### High-Availability Cluster

```yaml
proxmox_cluster:
  enabled: true
  name: "ha-cluster"
  ha_enabled: true
  corosync:
    redundant_ring: true
  resource_distribution:
    auto_failback: true
```

## Best Practices

1. **Network Configuration**
   - Use dedicated network interfaces for cluster communication
   - Configure redundant network links for high availability
   - Properly secure cluster communication with network restrictions

2. **Quorum and Voting**
   - Use an odd number of nodes for better quorum decisions
   - Configure appropriate quorum votes based on cluster size
   - Consider node weights in mixed-capability clusters

3. **Resource Distribution**
   - Set appropriate migration timeouts based on resource sizes
   - Configure auto-failback based on your HA requirements
   - Limit parallel migrations to prevent network congestion

4. **Monitoring**
   - Enable monitoring for better cluster health visibility
   - Set appropriate monitoring intervals
   - Configure alerts for critical cluster events

## Troubleshooting

Common issues and their solutions:

1. **Node Join Failures**
   - Check network connectivity
   - Verify corosync configuration
   - Ensure consistent cluster names

2. **Quorum Issues**
   - Verify node count and quorum votes
   - Check network connectivity
   - Review corosync logs

3. **Resource Distribution Problems**
   - Check migration timeouts
   - Verify network bandwidth
   - Review resource constraints 
