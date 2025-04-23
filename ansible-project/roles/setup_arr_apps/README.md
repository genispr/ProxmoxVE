# Ansible Role: Setup ARR Apps

This role:
- Creates an LXC container for media apps.
- Sets up Docker with an optional Docker Compose plugin.
- Deploys and configures media aggregation applications: Prowlarr, Sonarr, Radarr, Lidarr, Bazarr, Readarr, and Recyclarr.

## Requirements
- Ansible 2.9+
- Proxmox environment with LXC support

## Role Variables
Refer to the defaults file for container and application settings.
