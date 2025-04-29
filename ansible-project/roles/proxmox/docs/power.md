# Proxmox Power Management Configuration

This document describes the power management configuration options available in the Proxmox role.

## Overview

The power management configuration controls CPU frequency scaling, thermal management, UPS settings, and various power-saving features.

## Configuration Options

### CPU Frequency Management

```yaml
proxmox_power:
  cpu_freq:
    governor: "schedutil"
    min_speed: "800MHz"
    max_speed: "auto"
```

Available governors:
- `schedutil`: Uses scheduler information (balanced performance/power)
- `performance`: Maximum frequency (highest performance)
- `powersave`: Minimum frequency (maximum power savings)
- `ondemand`: Aggressive dynamic scaling based on load
- `conservative`: Gradual dynamic scaling based on load

### ACPI Configuration

```yaml
acpi:
  enabled: true
  events: ["powerbtn", "lid", "ac", "battery"]
```

- `enabled`: Enable/disable ACPI support
- `events`: List of ACPI events to monitor

### UPS Configuration

```yaml
ups:
  enabled: false
  driver: "usbhid-ups"
  port: "auto"
  monitor_mode: "master"
  shutdown_level: 20
```

- `enabled`: Enable UPS support
- `driver`: UPS driver type
- `port`: UPS connection port
- `monitor_mode`: UPS monitoring mode
- `shutdown_level`: Battery level for shutdown

### Power Saving Options

```yaml
saving:
  disk:
    spindown_time: 240
    apm_level: 127
  usb_suspend: true
  pci_pm: true

pcie_aspm_policy: "performance"
```

- `disk`: Disk power management settings
- `usb_suspend`: Enable USB device suspension
- `pci_pm`: Enable PCI power management
- `pcie_aspm_policy`: PCIe ASPM policy

### Disk Power Management

```yaml
disks:
  - name: "sda"
    power_control: "on"
    scheduler: "none"
  - name: "sdb"
    power_control: "on"
    scheduler: "none"
```

- `name`: Disk device name
- `power_control`: Power management state
- `scheduler`: I/O scheduler

### Network Interface Power Management

```yaml
network_interfaces:
  - name: "eth0"
    power_control: "on"
  - name: "eth1"
    power_control: "on"
```

- `name`: Interface name
- `power_control`: Power management state

### Thermal Management

```yaml
thermal_mode: "enabled"
thermal:
  cpu_temp_max: 80
  cpu_temp_crit: 95
  gpu_temp_max: 85
  gpu_temp_crit: 100
```

- `thermal_mode`: Enable/disable thermal management
- `cpu_temp_max`: Maximum CPU temperature
- `cpu_temp_crit`: Critical CPU temperature
- `gpu_temp_max`: Maximum GPU temperature
- `gpu_temp_crit`: Critical GPU temperature

### Fan Control

```yaml
fan_mode: "auto"
fan:
  min_speed: 20
  max_speed: 100
  temp_thresholds:
    - temp: 40
      speed: 30
    - temp: 50
      speed: 50
    - temp: 60
      speed: 70
    - temp: 70
      speed: 100
```

- `fan_mode`: Fan control mode
- `min_speed`: Minimum fan speed percentage
- `max_speed`: Maximum fan speed percentage
- `temp_thresholds`: Temperature-based speed settings

## Usage Examples

### Performance-Focused Configuration

```yaml
proxmox_power:
  cpu_freq:
    governor: "performance"
    min_speed: "2GHz"
  pcie_aspm_policy: "performance"
  thermal:
    cpu_temp_max: 85
    cpu_temp_crit: 95
```

### Power-Saving Configuration

```yaml
proxmox_power:
  cpu_freq:
    governor: "powersave"
    max_speed: "2.5GHz"
  saving:
    disk:
      spindown_time: 300
    usb_suspend: true
    pci_pm: true
  pcie_aspm_policy: "powersave"
```

## Best Practices

1. **CPU Power Management**
   - Choose appropriate governor for workload
   - Set realistic frequency limits
   - Monitor CPU temperature

2. **Storage Power Management**
   - Configure appropriate spindown times
   - Use compatible I/O schedulers
   - Monitor disk health

3. **Thermal Management**
   - Set appropriate temperature thresholds
   - Configure fan curves
   - Monitor system temperatures

4. **UPS Integration**
   - Configure appropriate shutdown levels
   - Test UPS functionality
   - Monitor battery health

## Monitoring

### Power Monitoring

```yaml
monitoring:
  enabled: true
  interval: 60
  tools:
    - powertop
    - powerstat
```

- `enabled`: Enable power monitoring
- `interval`: Monitoring interval
- `tools`: Monitoring tools to use

## Troubleshooting

Common issues and their solutions:

1. **CPU Performance Issues**
   - Check governor settings
   - Verify frequency limits
   - Monitor thermal throttling

2. **Thermal Problems**
   - Check fan operation
   - Verify temperature thresholds
   - Monitor system cooling

3. **Power Saving Issues**
   - Verify device compatibility
   - Check power management settings
   - Monitor power consumption

4. **UPS Problems**
   - Check UPS connectivity
   - Verify driver configuration
   - Test shutdown procedure

## Security Considerations

1. **ACPI Events**
   - Configure appropriate event handlers
   - Monitor event logs
   - Restrict event access

2. **Power Management Access**
   - Configure appropriate permissions
   - Monitor power-related commands
   - Restrict management access

3. **UPS Security**
   - Secure UPS communication
   - Configure appropriate access controls
   - Monitor UPS status changes 
