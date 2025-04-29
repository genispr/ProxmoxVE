# Proxmox Repository Configuration

This document describes the repository configuration options available in the Proxmox role.

## Overview

The repository configuration manages package sources, enterprise and community repositories, and package management settings for both Proxmox VE and PBS.

## Configuration Options

### Proxmox VE Repository Configuration

```yaml
proxmox_repositories:
  pve:
    enterprise: false      # Enable enterprise repository (requires subscription)
    no_subscription: true  # Enable community repository
    test: false           # Enable test repository
    version: "{{ ansible_distribution_release }}"  # Must be "bullseye" or "bookworm"
```

- `enterprise`: Enable enterprise repository (subscription required)
- `no_subscription`: Enable community repository
- `test`: Enable test repository
- `version`: Debian version codename

### Proxmox Backup Server Repository Configuration

```yaml
pbs:
  enterprise: false      # Enable enterprise repository (requires subscription)
  no_subscription: true  # Enable community repository
  test: false           # Enable test repository
  version: "{{ ansible_distribution_release }}"  # Must be "bullseye" or "bookworm"
```

- `enterprise`: Enable enterprise repository (subscription required)
- `no_subscription`: Enable community repository
- `test`: Enable test repository
- `version`: Debian version codename

### General Repository Configuration

```yaml
general:
  components: ["main", "contrib", "non-free", "non-free-firmware"]
  security_updates: true
  backports: false
```

- `components`: Repository components to enable
- `security_updates`: Enable security updates
- `backports`: Enable backports repository

### Package Management

```yaml
packages:
  update_cache: true
  cache_valid_time: 3600
  allow_downgrade: false
  autoremove: true
  autoclean: true
```

- `update_cache`: Update package cache
- `cache_valid_time`: Cache validity period
- `allow_downgrade`: Allow package downgrades
- `autoremove`: Remove unused packages
- `autoclean`: Clean package cache

### Repository Keys

```yaml
keys:
  pve:
    url: "https://enterprise.proxmox.com/debian/proxmox-release-{{ ansible_distribution_release }}.gpg"
    state: present
  pbs:
    url: "https://enterprise.proxmox.com/debian/proxmox-release-{{ ansible_distribution_release }}.gpg"
    state: present
```

- `url`: Repository key URL
- `state`: Key state (present/absent)

### Repository Backup

```yaml
backup:
  enabled: true
  path: "/etc/apt/sources.list.backup"
  keep: true
```

- `enabled`: Enable repository backup
- `path`: Backup file path
- `keep`: Keep backup after changes

## Usage Examples

### Community Edition Setup

```yaml
proxmox_repositories:
  pve:
    enterprise: false
    no_subscription: true
    test: false
  pbs:
    enterprise: false
    no_subscription: true
    test: false
  general:
    components: ["main", "contrib", "non-free"]
    security_updates: true
```

### Enterprise Edition Setup

```yaml
proxmox_repositories:
  pve:
    enterprise: true
    no_subscription: false
    test: false
  pbs:
    enterprise: true
    no_subscription: false
    test: false
  packages:
    update_cache: true
    cache_valid_time: 7200
```

## Best Practices

1. **Repository Management**
   - Use stable repositories
   - Keep consistent configurations
   - Regular repository updates
   - Backup repository files

2. **Package Management**
   - Regular cache updates
   - Clean unused packages
   - Monitor package versions
   - Validate package signatures

3. **Security Updates**
   - Enable security repositories
   - Regular security updates
   - Monitor security advisories
   - Test updates in staging

4. **Repository Keys**
   - Verify key authenticity
   - Regular key rotation
   - Monitor key expiration
   - Secure key storage

## Troubleshooting

Common issues and their solutions:

1. **Repository Access Issues**
   - Check network connectivity
   - Verify repository URLs
   - Review GPG key status
   - Check subscription status

2. **Package Problems**
   - Update package cache
   - Check package dependencies
   - Verify repository priorities
   - Review package conflicts

3. **Update Failures**
   - Check disk space
   - Verify repository status
   - Review update logs
   - Check package holds

4. **Key Management Issues**
   - Verify key imports
   - Check key permissions
   - Review key expiration
   - Monitor key updates

## Repository Maintenance

### Cache Management

```yaml
cache:
  max_size: "1G"
  min_free: "1G"
  cleanup_interval: "7d"
  keep_pkgs: 2
```

### Repository Health Checks

```yaml
health_check:
  enabled: true
  interval: "1d"
  checks:
    - repository_access
    - key_validity
    - cache_status
    - package_consistency
```

## Security Considerations

1. **Repository Security**
   - Use HTTPS repositories
   - Verify package signatures
   - Monitor repository changes
   - Regular security audits

2. **Access Control**
   - Restrict repository access
   - Secure key storage
   - Monitor repository usage
   - Log configuration changes

3. **Update Security**
   - Test updates first
   - Maintain update policies
   - Monitor security fixes
   - Keep change logs 
