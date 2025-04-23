# Vaultwarden Role

This role installs and configures Vaultwarden on a Debian-based system.

## Requirements
- A Debian/Ubuntu system.
- Rust toolchain will be installed if not available.

## Role Variables
You can override variables as needed:
- ADMIN_TOKEN: Leave empty to be generated later.
- DATABASE_URL: Defaults to a local SQLite database.

## Example Playbook
```yaml
- hosts: vault_servers
  become: yes
  roles:
    - vaultwarden
```

## License
MIT
