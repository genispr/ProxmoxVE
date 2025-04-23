# Ansible Role: netbird

This role installs the NetBird UI on a Debian/Ubuntu system (for example, in a Proxmox LXC container) and (optionally) updates the container configuration to allow NetBird’s required devices.

## Requirements

- When used in a Proxmox VE environment, you can either supply an existing container ID via `netbird_ctid` or allow the role to create a new LXC container if `create_lxc_container` is set to true.
- If Docker is required inside the container, set `setup_docker_lxc` to true.

## Role Variables

- `create_lxc_container` (boolean): When true, the role will trigger tasks to create a new LXC container. Default is true.
- `setup_docker_lxc` (boolean): When true, additional tasks will set up Docker in the LXC container. Default is true.
- `netbird_ctid` (optional): The Proxmox container ID (e.g. 101) for which NetBird configuration should be applied. Used when not creating a new container.

## Example Playbook

```yaml
- hosts: netbird_container
  become: true
  roles:
    - role: netbird
      vars:
        create_lxc_container: true
        setup_docker_lxc: true
        netbird_ctid: 101
```

## License

MIT
