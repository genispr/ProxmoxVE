# Ansible Role: Setup Jellyfin in LXC

This role installs and configures the Jellyfin Media Server within an LXC container on Debian or Ubuntu systems.

## Features

- Installs the Jellyfin media server using its official repository for seamless updates.
- Ensures the Jellyfin service is actively running and enabled at startup.
- Optionally installs and configures supporting packages such as ffmpeg for transcoding and Midnight Commander for file management.
- **Optional Intel Quick Sync Video (QSV) Hardware Acceleration:**
  - Configures necessary drivers (choosing between `intel-media-va-driver` or `intel-media-va-driver-non-free` based on your OS version).
  - Adds the `jellyfin` user to the `render` group for access to GPU devices.
  - Performs a validation check using `vainfo` to help diagnose potential issues.
  - **Important:** Requires proper passthrough of `/dev/dri` from the Proxmox host to the LXC container. Configure this in your LXC settings.

## Requirements

- Ansible 2.9 or higher.
- A Debian or Ubuntu-based LXC container.
- Execute the role *inside* the target container (e.g., via `ansible_connection=lxc`).
- For Intel QSV: Ensure that `/dev/dri` is passed through to the container.

## Role Variables

Refer to `defaults/main.yml` for variable details. Key variables include:

- `jellyfin_install_ffmpeg`: (Default: `true`) Enable ffmpeg installation.
- `jellyfin_install_mc`: (Default: `false`) Toggle installation of Midnight Commander.
- `jellyfin_setup_intel_qsv`: (Default: `false`) Enable optional Intel QSV acceleration.
- `jellyfin_package_version`: (Default: `latest`) Install the latest Jellyfin release or a specific version.
- `jellyfin_synchronize_timezone`: (Default: `false`) Sync container timezone with the host.
- `jellyfin_manage_web_package`: (Default: `false`) Manage the Jellyfin web package explicitly.

## Dependencies

None

## Example Playbook

```yaml
- hosts: your_lxc_host # Target the LXC container
  become: true  # Jellyfin installation requires root
  gather_facts: true # Usually helpful for distribution info

  roles:
    - role: setup_jellyfin_lxc
      vars:
        jellyfin_install_mc: true # Install mc too
        jellyfin_setup_intel_qsv: true # Try to set up Intel QSV (if /dev/dri passthrough is configured)
        jellyfin_package_version: "10.8.10" # Install a specific version
```

**Note:** For Intel QSV, ensure `/dev/dri` passthrough is correctly configured.
