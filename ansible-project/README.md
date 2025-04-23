# Vaultwarden Role

This role installs and configures Vaultwarden (an unofficial Bitwarden server) on a Debian-based system.

## Requirements
- A Debian/Ubuntu system.
- The role will install Rust (via rustup) if not already installed.
- Ensure that you have sufficient privileges to build software and manage systemd services.

## Role Variables
The following variables can be overridden in your inventory or playbook:
- **admin_token**: Initial Vaultwarden admin token (default empty and can be generated later).
- **database_url**: Defaults to a local SQLite database (`sqlite:///opt/vaultwarden/data/vaultwarden.db`).
- **vaultwarden_install_path**: Installation root, default is `/opt/vaultwarden`.
- **vaultwarden_user** and **vaultwarden_group**: Default to `vaultwarden`.

_If you need to reset or update the admin token, the role’s tasks (or an external update script) should modify the file `/opt/vaultwarden/.env` and optionally update `/opt/vaultwarden/data/config.json`._

## Example Playbook
Below is an example of installing Vaultwarden inside an LXC container.
```yaml
- hosts: proxmox_node
  become: yes
  roles:
    - role: create_lxc_container
      vars:
        lxc_id: 205
        lxc_hostname: vaultwarden-container
        lxc_os_template: local:vztmpl/debian-12-standard_12.2-1_amd64.tar.zst
        lxc_cores: 2
        lxc_memory: 2048
        lxc_disk_size: 10G
        lxc_network_config:
          net0: name=eth0,bridge=vmbr0,ip=dhcp
- hosts: "{{ hostvars['localhost']['lxc_205_ip'] }}"
  become: yes
  roles:
    - vaultwarden
```

## License
MIT
