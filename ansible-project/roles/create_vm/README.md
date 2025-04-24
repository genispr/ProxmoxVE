# create_vm Role

This role creates a generic Proxmox VM. It:
- Checks prerequisites (root, architecture, Proxmox version)
- Validates storage pools
- Optionally downloads and extracts a disk image (if create_vm_download_image is true)
- Creates the VM, allocates disk, imports the disk image (if applicable), configures the VM, resizes disk, and starts the VM.
