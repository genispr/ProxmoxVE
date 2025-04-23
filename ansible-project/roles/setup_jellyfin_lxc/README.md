# Ansible Role: Setup Jellyfin in LXC

This role installs and configures Jellyfin Media Server within a Debian or Ubuntu-based LXC container.

## Features

- Installs Jellyfin from the official repository for easy updates.
- Ensures the Jellyfin service is running and enabled.
- Installs `ffmpeg` (highly recommended for transcoding) - optional.
- Optionally installs `mc` (Midnight Commander), a user-friendly file manager.
- **Optional Intel Quick Sync Video (QSV) Hardware Acceleration:**
    - Installs necessary drivers (`intel-media-va-driver` or `intel-media-va-driver-non-free`).
    - Adds the `jellyfin` user to the `render` group for access to `/dev/dri`.
    - Includes a `vainfo` check to verify driver and VA-API setup (for informational purposes).
    - **Important:** QSV requires passthrough of `/dev/dri` from the Proxmox host to the LXC container. This should be configured in the LXC settings (e.g., using the `create_lxc_container` role, which is recommended).

## Requirements

- Ansible 2.9 or higher.
- Debian or Ubuntu-based LXC container.
- Assumes the role is executed *inside* the target container (e.g., via `ansible_connection=lxc`).
- **For Intel QSV:** Requires `/dev/dri` passthrough from the Proxmox host to the container (configure in LXC settings).

## Role Variables

See `defaults/main.yml` for a complete list of configurable variables and their default values.

**Key Variables:**

- `jellyfin_install_ffmpeg`: (Default: `true`) Install the `ffmpeg` package.
- `jellyfin_install_mc`: (Default: `false`) Install the `mc` package.
- `jellyfin_setup_intel_qsv`: (Default: `false`) Attempt to configure Intel QSV hardware acceleration.
- `jellyfin_package_version`: (Default: `latest`) Specifies the version of Jellyfin to install. Use 'latest' for the newest release, or specify a version number (e.g., "10.8.10") to pin to a particular release.
- `jellyfin_synchronize_timezone`: (Default: `false`) If `true`, sets the container's timezone to match the host's.
- `jellyfin_manage_web_package`: (Default: `false`) If `true`, explicitly manages the `jellyfin-web` package and web directory.

## Dependencies

None

## Example Playbook
```
yaml
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
**Important Notes:**

- **Hardware Acceleration:** If using `jellyfin_setup_intel_qsv: true`, ensure you have properly configured device passthrough for `/dev/dri` in your LXC container settings on the Proxmox host. Otherwise, this setup will likely fail or be ineffective. Consult the Proxmox documentation for instructions on device passthrough.
- **Debian Non-Free Components:** For Debian systems, the role automatically handles enabling the `non-free` (for Debian versions < 12) or `non-free-firmware` (for Debian versions >= 12) APT component if Intel QSV setup is enabled. This is necessary to install the required drivers.

## License

MIT