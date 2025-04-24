# Docker VM Role

This role installs and configures Docker Engine inside a virtual machine. It downloads and runs the official Docker installer, writes a custom daemon configuration, and optionally installs the Docker Compose plugin and Portainer container.

## Features
- Installs required prerequisites.
- Uses the official get-docker.sh script to install Docker.
- Configures the Docker daemon with a custom configuration.
- Optionally installs Docker Compose.
- Optionally deploys a Portainer container for Docker management.

## Role Variables
Please refer to `defaults/main.yml` for a list of configurable variables.

## Example Playbook
```yaml
- hosts: docker_vm_host
  become: yes
  roles:
    - docker_vm
```

## License
MIT
...
