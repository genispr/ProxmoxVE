# Ansible Role: OpnSense VM

This role creates and configures a VM for OpnSense on Proxmox VE. It manages creation, disk allocation, and VM configuration.

## Role Variables

- opnsense_vm_id: Unique ID for the OpnSense VM.
- opnsense_hostname: VM hostname.
- opnsense_cpu_cores: Number of CPU cores.
- opnsense_ram: RAM in MB.
- opnsense_storage: Storage pool name.
- opnsense_bridge: Network bridge name.
- opnsense_mac: MAC address for the VM.
- opnsense_image_path: Path to the OpnSense disk image.

## Example Playbook

```yaml
- hosts: proxmox
  become: true
  roles:
    - role: opnsense_vm
      vars:
        opnsense_vm_id: 101
        opnsense_hostname: "OpnSense"
        opnsense_cpu_cores: 2
        opnsense_ram: 2048
        opnsense_storage: "local-lvm"
        opnsense_bridge: "vmbr0"
        opnsense_mac: "02:00:00:00:00:01"
        opnsense_image_path: "/path/to/opnsense-disk-image.qcow2"
```

## License

MIT
