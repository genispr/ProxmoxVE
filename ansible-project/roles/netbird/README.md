# Ansible Role: netbird

This role installs the NetBird UI in a Proxmox LXC container.
- Supply an existing container ID via `netbird_ctid`.  


## Requirements

- Supply an existing container ID via `netbird_ctid`.

## Role Variables

- `netbird_ctid`: LXC container ID to use.

## Example Playbook

```yaml
- hosts: netbird_container
  become: true
  roles:
    - role: netbird
      vars:
        netbird_ctid: 101
```

## License

MIT
