# install_caddy Role

This role installs and configures Caddy as a reverse proxy for LXC containers in local environments without a bought domain.

## Variables

- `caddy_domain_suffix`: The domain suffix to use (default: ".local").
- `caddy_proxies`: List of proxies. Example:
  [{ hostname: "container1", ip: "192.168.1.10", port: 80 }]

## Usage

Include this role in your playbook to install and configure Caddy:
```yaml
- hosts: all
  roles:
    - install_caddy
```

...existing documentation...
