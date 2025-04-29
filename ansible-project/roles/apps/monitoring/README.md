# Monitoring Role

## Overview
Manages the deployment and configuration of Prometheus, Grafana, and Node Exporter.

## Features
- Automated SSL certificate management
- Service discovery via Docker labels
- Pre-configured dashboards and alerts
- Secure metrics authentication

## Variables
- `monitoring_ssl_enabled`: Enable SSL/TLS (default: true)
- `monitoring_auth_enabled`: Enable basic auth (default: true)
- `monitoring_admin_password`: Grafana admin password

## Alert Configuration
- `monitoring_alerts_enabled`: Enable alerting (default: true)
- `monitoring_alert_receivers`: List of notification targets
- `monitoring_alert_slack_webhook`: Slack webhook URL for notifications

## Usage Examples

### Basic Setup
```yaml
- hosts: proxmox
  roles:
    - role: apps/monitoring
      vars:
        monitoring_ssl_enabled: true
        monitoring_auth_enabled: true
```

### With Alert Configuration
```yaml
- hosts: proxmox
  roles:
    - role: apps/monitoring
      vars:
        monitoring_alerts_enabled: true
        monitoring_alert_receivers:
          - name: slack
            slack_configs:
              - webhook_url: "{{ monitoring_alert_slack_webhook }}"
```

## Dependencies
- Docker role
- Network role
