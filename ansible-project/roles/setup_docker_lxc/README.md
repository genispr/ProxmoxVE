# Ansible Role: Setup Docker in LXC

This role installs Docker Engine inside an LXC container running Debian or Ubuntu.
It utilizes the official Docker installation script and ensures the Docker service
is properly configured, started, and optionally enhanced with the Docker Compose V2 plugin.

## Features

- Installs the latest stable Docker Engine using the official `get-docker.sh` script.
- Manages required prerequisites like `curl` and `gpg`.
- Ensures the Docker daemon is running and enabled.
- Optionally installs the Docker Compose V2 plugin for improved compose support.

## Requirements

- Ansible 2.9 or higher.
- Target LXC container must be Debian or Ubuntu-based (uses `apt`).
- Target LXC container must have internet access to download Docker.
- Assumes running *inside* the target LXC container (e.g., using `ansible_connection=lxc` or after adding the container to inventory).

## Role Variables

- `install_docker_compose_plugin`: (Default: `true`) Disable to skip Docker Compose V2 plugin installation.
- `docker_user`: (Optional) Add this user to the `docker` group for privilege-free Docker command usage.

## Dependencies

None.

## Example Playbook Integration

This role is typically used *after* the `create_lxc_container` role has run and the new container has been added to the Ansible inventory.

```yaml
- hosts: your_proxmox_node
  become: no
  gather_facts: no
  vars_prompt:
    # ... (Vault prompt for Proxmox API password)
  roles:
    - role: create_lxc_container
      vars:
        proxmox_api_password: "{{ vault_proxmox_api_password }}"
        lxc_id: 205
        lxc_hostname: docker-runner
        # ... other container settings ...

- name: Configure Docker Host
  hosts: docker-runner # Target the newly created container hostname
  become: yes # Docker installation requires root privileges
  gather_facts: yes # Gather facts inside the container
  pre_tasks:
    - name: Wait for system readiness after boot
      ansible.builtin.wait_for_connection:
        delay: 10
        timeout: 300

  roles:
    - role: setup_docker_lxc
      vars:
        # docker_user: myappuser # Optional: Add user to docker group
        install_docker_compose_plugin: true

  post_tasks:
    - name: Verify Docker installation
      ansible.builtin.command: docker --version
      register: docker_version
      changed_when: false
    - name: Display Docker version
      ansible.builtin.debug:
        var: docker_version.stdout
```

**Note on Inventory:** You need a way to add the newly created LXC container (`docker-runner` in the example) to your Ansible inventory *between* the two plays. This can be done using:

1.  **`add_host` module:** Add the container dynamically in the first play.
2.  **Dynamic Inventory Script:** Use a Proxmox dynamic inventory script that automatically discovers running containers.
3.  **Manual Inventory Update:** Update your inventory file manually before running the second play.

---

## License

MIT
