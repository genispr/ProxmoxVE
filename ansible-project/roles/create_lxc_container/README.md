# Ansible Role: Proxmox LXC Container Creator

This role creates or updates LXC containers on Proxmox Virtual Environment (PVE) servers. It's designed to be a robust base for deploying applications, particularly Docker containers, within LXC.

## Features

- Creates or ensures an LXC container exists with specified parameters.
- Configurable hostname, ID, OS template, resources (CPU, memory, disk), networking (static or DHCP).
- Enables features recommended for running Docker inside LXC (`nesting: 1`, `keyctl: 1`).
- Starts the container after creation/update.
- Waits for SSH readiness after container start (useful for subsequent configuration tasks).
- Fetches and stores the container's IP address as an Ansible fact (`lxc_<vmid>_ip`).

## Requirements

- Ansible 2.9 or higher.
- Proxmox VE server with API access configured.
- **Ansible Collection:** `community.general` must be installed (`ansible-galaxy collection install community.general`).
- **Proxmox OS Templates:** Ensure the desired OS template (defined in `lxc_os_template`) exists on your Proxmox node's designated storage.
    - List available templates: `pveam available`
    - Find system templates: `pveam available --section system`
    - Download a template (e.g., Debian 12): `pveam download local debian-12-standard` (replace `local` with your template storage ID).

## Security Considerations

- **Root Password:** The default `lxc_password` is highly insecure. **DO NOT use the default in production.**
    - **Recommendation:** Use Ansible Vault to encrypt the password (`ansible-vault encrypt_string 'YOUR_STRONG_PASSWORD' --name 'lxc_password'`).
    - Alternatively, set strong passwords per host/group in your inventory vars.
    - **Best Practice:** Configure SSH key-based authentication within the container using a subsequent role/task instead of relying on passwords.
- **Proxmox API Password/Token:** The `proxmox_api_password` variable should contain your Proxmox API user's password or an API token secret. **DO NOT store this directly in `defaults/main.yml` for production.**
    - **Recommendation:** Use Ansible Vault (`ansible-vault encrypt_string 'YOUR_API_SECRET' --name 'proxmox_api_password'`) or environment variables.
- **Unprivileged Containers:** The role defaults to `lxc_unprivileged: true`. While generally recommended, running Docker might require specific permissions or configurations. If you encounter permission issues with Docker inside an unprivileged container, you might need to adjust AppArmor/Seccomp profiles or consider using a privileged container (`lxc_unprivileged: false`) if absolutely necessary, understanding the security implications.

## Role Variables

Refer to `defaults/main.yml` for a complete list of configurable variables.

## Example Playbook

```yaml
- hosts: your_proxmox_node
  become: no # Tasks run against the Proxmox API, not the host OS directly
  gather_facts: no # Usually not needed for API operations

  vars_prompt:
    - name: vault_proxmox_api_password
      prompt: "Enter Proxmox API Password/Token Secret"
      private: yes

  roles:
    - role: create_lxc_container
      vars:
        lxc_enable_dri_passthrough: true # Enable hardware acceleration
        proxmox_api_password: "{{ vault_proxmox_api_password }}"
        lxc_id: 201
        lxc_hostname: docker-host-01
        lxc_os_template: local:vztmpl/debian-12-standard_12.2-1_amd64.tar.zst
        lxc_cores: 4
        lxc_memory: 4096
        lxc_disk_size: 50G
        lxc_network_config:
          net0: name=eth0,bridge=vmbr0,ip=192.168.1.201/24,gw=192.168.1.1
        # lxc_password: "YOUR_SECURE_PASSWORD" # Or use Vault

# Example of using the created container later in the same play
- hosts: your_proxmox_node # This play targets the Proxmox node to get the IP fact
  become: no
  gather_facts: no
  tasks:
    - name: Show the IP of the created container
      ansible.builtin.debug:
        msg: "Container 201 IP is {{ lxc_201_ip }}"
      when: lxc_201_ip is defined

# You would typically have another play targeting the new container
# using add_host or dynamic inventory to configure it further.
# - hosts: docker-host-01 # Assuming inventory is updated
#   roles:
#     - role: setup_docker
#     - role: deploy_my_app
```

## License

MIT
