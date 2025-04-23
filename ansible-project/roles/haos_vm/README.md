# HAOS VM Role

This Ansible role creates and configures a Home Assistant OS VM on ProxmoxVE.

## Features
- Performs root and architecture checks.
- Verifies compatible Proxmox VE version.
- Generates a unique MAC address.
- Retrieves the next available VMID.
- Validates available image storage pools.
- Downloads and extracts the HAOS disk image.
- Creates the VM (using `qm create`), imports the disk (via `qm importdisk`),
  and configures the VM with appropriate description.
- Optionally starts the VM after creation.

## Variables
Refer to `defaults/main.yml` for configuration. Key variables include:
- **vmid**: Next available VM ID.
- **hostname**: Hostname for the VM.
- **cores**: Number of CPU cores to allocate.
- **ram**: RAM (in MiB).
- **disk_size**: Disk size (e.g., "32G").
- **bridge**: Network bridge to use (default is "vmbr0").
- **cpu_type**: CPU type settings (e.g., "-cpu host").
- **disk_cache**: Disk cache settings.
- **haos_version**: HAOS version (e.g., "stable").
- **haos_url**: URL for downloading the HAOS disk image.
- **start_vm**: Boolean; if true, the VM is started after creation.
- **dest_image**: Destination filename for the downloaded image.
- **extracted_image**: Filename after image extraction.

## Usage
Include this role in your playbook as follows:

```yaml
- hosts: proxmox
  become: true
  roles:
    - haos_vm
```

## License
MIT

## Author
Your Name or GitHub Username
