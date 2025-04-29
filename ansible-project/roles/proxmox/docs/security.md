# Proxmox Security Configuration

This document describes the security configuration options available in the Proxmox role.

## Overview

The security configuration manages system access control, firewall rules, authentication, and various security hardening features.

## Configuration Options

### SSH Configuration

```yaml
proxmox_security:
  ssh:
    port: 22
    permit_root_login: "prohibit-password"
    password_authentication: false
    max_auth_tries: 3
    allow_users: ["root"]
    allow_groups: ["sudo"]
    public_keys: []
```

- `port`: SSH port number
- `permit_root_login`: Root login policy
- `password_authentication`: Enable password auth
- `max_auth_tries`: Maximum auth attempts
- `allow_users`: Allowed SSH users
- `allow_groups`: Allowed SSH groups
- `public_keys`: SSH public keys

### PAM Limits

```yaml
limits:
  nproc: 65535
  nofile: 1048576
  memlock: "unlimited"
```

- `nproc`: Maximum number of processes
- `nofile`: Maximum number of open files
- `memlock`: Memory lock limit

### Firewall Configuration

```yaml
proxmox_firewall:
  enabled: true
  log_level: "nolog"
  policy:
    in: "DROP"
    out: "ACCEPT"
  rules:
    - direction: "in"
      action: "ACCEPT"
      proto: "tcp"
      dport: "22"
      comment: "SSH access"
    - direction: "in"
      action: "ACCEPT"
      proto: "tcp"
      dport: "8006"
      comment: "Web UI access"
```

- `enabled`: Enable firewall
- `log_level`: Logging level
- `policy`: Default policies
- `rules`: Firewall rules list

### Fail2ban Configuration

```yaml
proxmox_fail2ban:
  enabled: true
  bantime: "24h"
  findtime: "24h"
  maxretry: 3
  ignoreip: ["127.0.0.1/8"]
  services:
    - name: "sshd"
      enabled: true
      port: "ssh"
      filter: "sshd"
      logpath: "/var/log/auth.log"
    - name: "proxmox"
      enabled: true
      port: "https,http,8006"
      filter: "proxmox"
      logpath: "/var/log/daemon.log"
```

- `enabled`: Enable Fail2ban
- `bantime`: Ban duration
- `findtime`: Time window for maxretry
- `maxretry`: Maximum retry attempts
- `ignoreip`: IP addresses to ignore
- `services`: Service-specific settings

### SELinux Configuration

```yaml
proxmox_selinux:
  state: "disabled"  # enforcing, permissive, or disabled
  type: "targeted"
  policy: "targeted"
```

- `state`: SELinux state
- `type`: SELinux type
- `policy`: SELinux policy

### Kernel Security Configuration

```yaml
proxmox_kernel_security:
  enable_module_loading: true
  disable_modules: ["cramfs", "freevxfs", "jffs2", "hfs", "hfsplus", "udf"]
  sysrq: 0
  kexec_load_disabled: 1
  keys:
    maxkeys: 2000
    maxbytes: 2000000
```

- `enable_module_loading`: Allow module loading
- `disable_modules`: Blacklisted modules
- `sysrq`: SysRq key configuration
- `kexec_load_disabled`: Disable kexec loading
- `keys`: Kernel key settings

### Authentication Configuration

```yaml
proxmox_auth:
  realm: "pam"
  two_factor: false
  token_lifetime: "28800"
  max_attempts: 3
  lockout_window: "1h"
```

- `realm`: Authentication realm
- `two_factor`: Enable 2FA
- `token_lifetime`: Token validity period
- `max_attempts`: Maximum login attempts
- `lockout_window`: Account lockout duration

## Usage Examples

### Basic Security Setup

```yaml
proxmox_security:
  ssh:
    password_authentication: false
    max_auth_tries: 3
  firewall:
    enabled: true
    policy:
      in: "DROP"
      out: "ACCEPT"
```

### Enhanced Security Configuration

```yaml
proxmox_security:
  ssh:
    permit_root_login: "prohibit-password"
    password_authentication: false
  fail2ban:
    enabled: true
    bantime: "48h"
  selinux:
    state: "enforcing"
  auth:
    two_factor: true
```

## Best Practices

1. **Access Control**
   - Use SSH key authentication
   - Implement strong password policies
   - Enable two-factor authentication
   - Restrict root access

2. **Network Security**
   - Configure restrictive firewall rules
   - Enable intrusion detection
   - Monitor network traffic
   - Use secure protocols

3. **System Hardening**
   - Enable SELinux/AppArmor
   - Configure kernel security
   - Disable unnecessary services
   - Regular security updates

4. **Authentication Security**
   - Implement account lockouts
   - Use secure authentication realms
   - Monitor authentication attempts
   - Regular credential rotation

## Troubleshooting

Common issues and their solutions:

1. **SSH Access Issues**
   - Verify SSH configuration
   - Check firewall rules
   - Review authentication logs
   - Validate key permissions

2. **Firewall Problems**
   - Check rule syntax
   - Verify rule order
   - Monitor firewall logs
   - Test rule effectiveness

3. **Authentication Failures**
   - Check realm configuration
   - Verify user permissions
   - Review auth logs
   - Test 2FA setup

4. **SELinux Issues**
   - Check SELinux status
   - Review policy violations
   - Verify file contexts
   - Monitor audit logs

## Security Auditing

### Audit Configuration

```yaml
audit:
  enabled: true
  log_format: "raw"
  max_log_file: 8
  max_log_file_action: "rotate"
  space_left: 75
  space_left_action: "email"
  rules:
    - "-a always,exit -F arch=b64 -S execve -k exec"
    - "-w /etc/passwd -p wa -k identity"
    - "-w /etc/group -p wa -k identity"
```

### Security Scanning

```yaml
security_scan:
  enabled: true
  schedule: "weekly"
  tools:
    - rkhunter
    - lynis
    - clamav
  reports:
    email: "security@example.com"
    retention: "90d"
```

## Compliance

1. **Policy Enforcement**
   - Regular compliance checks
   - Policy documentation
   - Audit trail maintenance
   - Regular reviews

2. **Incident Response**
   - Incident documentation
   - Response procedures
   - Recovery plans
   - Post-incident analysis 
