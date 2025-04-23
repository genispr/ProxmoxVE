This role performs post-installation tasks on Proxmox VE servers, such as optionally enabling IOMMU, disabling the subscription nag message, and updating the system.

## Role Variables

* `disable_subscription_nag`: (Default: `false`) If set to `true`, disables the Proxmox VE no-subscription nag message in the web UI.
  **Warning:** While this removes the nag message, it is generally not recommended for production environments. A valid Proxmox VE subscription is the preferred way to support the project and gain access to updates and support.
* `update_upgrade_system`: (Default: `false`) If set to `true`, updates the APT package lists and performs a full system upgrade of the Proxmox VE server.
* `enable_iommu`: (Default: `false`) If set to `true`, the role will attempt to enable IOMMU by modifying the GRUB configuration and rebooting the system. This involves modifying the GRUB boot configuration and updating the kernel. The role automatically detects the CPU vendor (Intel or AMD) and adds the appropriate IOMMU parameter to the GRUB configuration. **Enabling IOMMU requires a system reboot, which the role will perform.**

## Handlers

* `reboot`: Reboots the system.

## Example Playbook

- hosts: your_proxmox_host
  become: true
  roles:
    - role: post_pve_install
      vars:
        enable_iommu: true  # Enable IOMMU
        disable_subscription_nag: true # Disable the subscription nag (use with caution)
        update_upgrade_system: true # Update and upgrade the system
