# LXC Networking Configuration Guide

This document describes the networking configuration options available for LXC containers in the Proxmox role.

## Table of Contents
- [Basic Configuration](#basic-configuration)
- [Network Templates](#network-templates)
- [Firewall Templates](#firewall-templates)
- [IP Tagging](#ip-tagging)
- [Advanced Configuration](#advanced-configuration)
- [Examples](#examples)
- [Best Practices](#best-practices)
- [Troubleshooting](#troubleshooting)
- [Monitoring and Logging](#monitoring-and-logging)

## Basic Configuration

Network interfaces for LXC containers are configured under the `proxmox_lxc.network` section in your inventory variables:

```yaml
proxmox_lxc:
  network:
    interfaces:
      - vmid: 100
        name: "eth0"
        type: "veth"
        bridge: "vmbr0"
        ip: "dhcp"
```

### Default Settings

The following default settings are provided:
- Bridge: `vmbr0`
- Interface Type: `veth`
- Firewall: enabled
- MTU: 1500
- Rate Limiting: disabled

### Required Dependencies

The role automatically ensures all required packages are installed:
- `iproute2`
- `bridge-utils`
- `net-tools`
- `iptables`

## Network Templates

Pre-configured network templates are available for common scenarios:

### Internal Network
```yaml
internal:
  type: "veth"
  bridge: "vmbr0"
  firewall: true
  ip: "dhcp"
```

### DMZ Network
```yaml
dmz:
  type: "veth"
  bridge: "vmbr1"
  firewall: true
  tag: 100  # VLAN tag
```

### Isolated Network
```yaml
isolated:
  type: "veth"
  bridge: "vmbr2"
  firewall: false
```

### High-Performance Network
```yaml
high_performance:
  type: "veth"
  bridge: "vmbr0"
  firewall: false
  mtu: 9000  # Jumbo frames
  rate: null  # No rate limiting
```

### Restricted Network
```yaml
restricted:
  type: "veth"
  bridge: "vmbr3"
  firewall: true
  rate: 1  # 1 MB/s limit
  ip: "dhcp"
```

## Firewall Templates

Common firewall rule sets are available as templates:

### Web Server Rules
```yaml
web:
  - pos: 1
    action: "ACCEPT"
    proto: "tcp"
    dport: "80,443"
    comment: "Allow HTTP/HTTPS"
```

### SSH Access Rules
```yaml
ssh:
  - pos: 1
    action: "ACCEPT"
    proto: "tcp"
    dport: "22"
    comment: "Allow SSH"
```

### Default Deny Rules
```yaml
deny_all:
  - pos: 999
    action: "REJECT"
    type: "in"
    comment: "Deny all incoming traffic"
```

### Database Rules
```yaml
database:
  - pos: 1
    action: "ACCEPT"
    proto: "tcp"
    dport: "3306,5432"
    comment: "Allow MySQL/PostgreSQL"
```

### Monitoring Rules
```yaml
monitoring:
  - pos: 1
    action: "ACCEPT"
    proto: "tcp"
    dport: "9100,9090"
    comment: "Allow Prometheus metrics"
```

## IP Tagging

IP tagging allows automatic tracking and management of container IP addresses:

```yaml
ip_tagging:
  enabled: false
  cidr_list:
    - 192.168.0.0/16
    - 172.16.0.0/12
    - 10.0.0.0/8
    - 100.64.0.0/10
  intervals:
    loop: 60
    fw_net_interface_check: 60
    lxc_status_check: -1
    force_update: 1800
```

### IP Tagging Features
- Automatic IP detection
- Dynamic firewall rule updates
- Support for IPv4 and IPv6
- Custom CIDR list configuration
- Configurable update intervals

## Advanced Configuration

### Network Interface Options

| Option | Description | Default | Notes |
|--------|-------------|---------|-------|
| type | Interface type (veth, bridge) | veth | veth is recommended for most cases |
| bridge | Bridge device | vmbr0 | Must exist on host |
| firewall | Enable firewall | true | Requires proper rule configuration |
| rate | Rate limit in MB/s | null | null = unlimited |
| mtu | MTU size | 1500 | 9000 for jumbo frames |
| tag | VLAN tag | null | 1-4094 |
| gateway | Default gateway | null | Optional for static IP |
| ip | IPv4 address/CIDR | dhcp | Static IP or dhcp |
| ip6 | IPv6 address/CIDR | null | Optional |

### Firewall Rule Options

| Option | Description | Required | Notes |
|--------|-------------|----------|-------|
| pos | Rule position | No | Lower = higher priority |
| action | ACCEPT, REJECT, DROP | Yes | REJECT sends response, DROP doesn't |
| type | in, out | No | Direction of traffic |
| proto | tcp, udp, icmp | No | Protocol to match |
| dport | Destination port(s) | No | Single port or range |
| sport | Source port(s) | No | Single port or range |
| source | Source IP/Network | No | CIDR format |
| dest | Destination IP/Network | No | CIDR format |
| comment | Rule description | No | Documentation |
| enable | Enable rule | No | Default: true |
| log | Log matches | No | Default: false |

## Examples

### Basic Container with DHCP
```yaml
proxmox_lxc:
  network:
    interfaces:
      - vmid: 100
        name: "eth0"
        type: "veth"
        bridge: "vmbr0"
        ip: "dhcp"
```

### Web Server in DMZ
```yaml
proxmox_lxc:
  network:
    interfaces:
      - vmid: 101
        name: "eth0"
        template: "dmz"
        firewall_rules:
          - template: "web"
          - template: "ssh"
          - template: "deny_all"
```

### Multi-Interface Container
```yaml
proxmox_lxc:
  network:
    interfaces:
      - vmid: 102
        name: "eth0"
        template: "internal"
        ip: "192.168.1.10/24"
      - vmid: 102
        name: "eth1"
        template: "isolated"
        ip: "10.0.0.10/24"
```

### High-Performance Database Server
```yaml
proxmox_lxc:
  network:
    interfaces:
      - vmid: 103
        name: "eth0"
        template: "high_performance"
        ip: "192.168.1.20/24"
        firewall_rules:
          - template: "database"
          - template: "monitoring"
          - template: "deny_all"
```

### Load-Balanced Web Cluster
```yaml
proxmox_lxc:
  network:
    interfaces:
      - vmid: 104
        name: "eth0"
        template: "internal"
        ip: "192.168.1.30/24"
        firewall_rules:
          - pos: 1
            action: "ACCEPT"
            proto: "tcp"
            dport: "80,443"
            source: "192.168.1.100/32"  # Load balancer IP
            comment: "Allow traffic from load balancer"
          - template: "deny_all"
```

### IPv6-Enabled Container
```yaml
proxmox_lxc:
  network:
    interfaces:
      - vmid: 105
        name: "eth0"
        template: "internal"
        ip: "192.168.1.40/24"
        ip6: "2001:db8::40/64"
        gateway6: "2001:db8::1"
```

## Best Practices

### Security
1. Always use firewall rules to restrict access
2. Follow the principle of least privilege
3. Document all custom rules
4. Use templates for consistent configuration
5. Regularly audit firewall rules

### Performance
1. Use jumbo frames (MTU 9000) for storage networks
2. Disable firewall on isolated networks
3. Configure rate limiting based on application needs
4. Use multiple interfaces for different traffic types
5. Monitor network performance

### Maintenance
1. Use meaningful naming conventions
2. Document network topology
3. Keep templates updated
4. Regular backup of network configuration
5. Monitor IP tagging logs

## Troubleshooting

### Common Issues

1. **Container Can't Access Network**
   - Check bridge configuration
   - Verify IP configuration
   - Check firewall rules
   - Ensure gateway is correct

2. **Performance Issues**
   - Check MTU settings
   - Verify rate limiting
   - Monitor network utilization
   - Check for packet loss

3. **Firewall Problems**
   - Check rule order
   - Verify rule syntax
   - Check logs for blocked traffic
   - Test with temporary rules

### Validation Commands

```bash
# Check network interface
ip addr show

# Test connectivity
ping -c 4 <ip>

# Check firewall rules
iptables -L -n -v

# Monitor network traffic
tcpdump -i <interface>

# Check routing
ip route show
```

### Logging

The role automatically configures logging for:
- Network interface changes
- Firewall rule updates
- IP tagging events
- DHCP assignments

Logs can be found in:
- `/var/log/syslog`
- `/var/log/proxmox-firewall.log`
- `/var/log/lxc-net.log` 

## Monitoring and Logging

### Network Monitoring

The role includes comprehensive network monitoring using Prometheus and Node Exporter:

```yaml
proxmox_monitoring:
  enabled: true
  metrics:
    - network_interfaces
    - firewall
    - bandwidth
    - tcp_stack
    - latency
  retention: "30d"
  scrape_interval: "15s"
```

#### Available Metrics

| Metric | Description | Default Interval | Notes |
|--------|-------------|------------------|-------|
| network_interfaces | Interface status and statistics | 15s | Basic interface metrics |
| firewall | Firewall rule matches and statistics | 60s | Rule-level statistics |
| bandwidth | Network bandwidth utilization | 30s | Per-interface bandwidth |
| errors | Network error counters | 15s | Error statistics |
| drops | Dropped packet counters | 15s | Packet loss metrics |
| tcp_stack | TCP/IP stack statistics | 30s | Connection states |
| latency | Network latency metrics | 30s | Queue length based |

#### Alerting Rules

The following alerts are preconfigured:

1. **High Network Errors**
   - Triggers when error rate exceeds 100/5min
   - Severity: Warning
   - Evaluation: 5m
   - Metric: `network:packet_errors:rate5m`

2. **Network Interface Down**
   - Triggers when interface status is down
   - Severity: Critical
   - Evaluation: 1m
   - Metric: `node_network_up`

3. **High Network Utilization**
   - Triggers when bandwidth exceeds 100MB/s
   - Severity: Warning
   - Evaluation: 10m
   - Metric: `network:interface_bandwidth:rate5m`

4. **High TCP Retransmits**
   - Triggers when retransmission rate exceeds 50/5min
   - Severity: Warning
   - Evaluation: 5m
   - Metric: `network:tcp_retransmits:rate5m`

5. **TCP Connection Exhaustion**
   - Triggers when connection usage exceeds 80%
   - Severity: Warning
   - Evaluation: 5m
   - Metric: `network:tcp_connection_usage:ratio`

6. **Network Interface Saturation**
   - Triggers when transmit queue usage exceeds 80%
   - Severity: Warning
   - Evaluation: 5m
   - Metric: `network:interface_saturation:ratio5m`

7. **Abnormal Network Latency**
   - Triggers on high queue length with active traffic
   - Severity: Warning
   - Evaluation: 5m
   - Metrics: Combined queue length and packet rate

8. **Firewall Rule Storm**
   - Triggers when rule hits exceed 1000/min
   - Severity: Warning
   - Evaluation: 5m
   - Metric: `node_firewall_rule_hits_total`

### Grafana Dashboard

The role includes a preconfigured Grafana dashboard with the following panels:

#### Network Overview
- Network bandwidth graphs (in/out)
- Error rate monitoring
- Interface status indicators
- MTU size gauges

#### Firewall Statistics
- Rule hit rates
- Dropped packet tracking
- Rule effectiveness analysis
- Traffic pattern visualization

#### Performance Metrics
- TCP connection tracking
- Network load analysis
- Latency monitoring
- Queue length tracking

#### Features
- Auto-refresh (30s default)
- Variable time range selection
- Interface filtering
- Instance selection
- Dark/light theme support
- Exportable graphs
- Customizable thresholds

### Performance Monitoring

Network performance metrics are collected and can be visualized:

1. **Bandwidth Utilization**
   ```promql
   rate(node_network_receive_bytes_total[5m]) +
   rate(node_network_transmit_bytes_total[5m])
   ```

2. **Error Rates**
   ```promql
   rate(node_network_receive_errs_total[5m]) +
   rate(node_network_transmit_errs_total[5m])
   ```

3. **Dropped Packets**
   ```promql
   rate(node_network_receive_drop_total[5m]) +
   rate(node_network_transmit_drop_total[5m])
   ```

4. **TCP Performance**
   ```promql
   # Retransmission rate
   rate(node_netstat_Tcp_RetransSegs[5m])
   
   # Connection states
   node_netstat_Tcp_CurrEstab
   
   # Failed attempts
   rate(node_netstat_Tcp_AttemptFails[5m])
   ```

5. **Interface Saturation**
   ```promql
   rate(node_network_transmit_queue_length[5m]) / 1000
   ```

### Monitoring Integration

The role integrates with common monitoring systems:

1. **Prometheus**
   - Automatic service discovery
   - Custom recording rules
   - Alert definitions
   - Metric relabeling
   - Multi-target scraping

2. **Grafana**
   - Pre-configured dashboards
   - Network overview panels
   - Performance graphs
   - Variable support
   - Export capabilities

3. **Node Exporter**
   - Network interface metrics
   - Protocol statistics
   - Connection states
   - Queue metrics
   - TCP/IP stack data

4. **Alert Manager**
   - Severity-based routing
   - Alert grouping
   - Notification templates
   - Escalation paths
