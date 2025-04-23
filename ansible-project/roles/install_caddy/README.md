# install_caddy Role

This role installs and configures Caddy as a reverse proxy for LXC containers in local environments without a purchased domain.

## Variables

- caddy_domain_suffix: The domain suffix used to construct proxy domains (default: ".local").
- caddy_proxies: A list of proxy definitions. Each entry should be a dictionary with:
    - hostname: The target container's hostname.
    - ip: The container's IP address.
    - port: The container's service port.
  Example:
    - { hostname: "container1", ip: "192.168.1.10", port: 80 }

## Usage

Include this role in your playbook:
```yaml
- hosts: all
  roles:
    - install_caddy
```

## Notes

- The Caddyfile is deployed to /etc/caddy/Caddyfile.
- When configuration changes, the "Reload Caddy" handler is triggered to restart the service.
