# Configure Timezone Role

This role configures the system timezone across different types of hosts and containers in a consistent manner.

## Requirements

- Ansible 2.9 or higher
- Root access to target systems
- `tzdata` package availability in system repositories

## Role Variables

| Variable | Default | Description |
|----------|---------|-------------|
| `timezone` | `UTC` | The timezone to set. Uses [TZ database names](https://en.wikipedia.org/wiki/List_of_tz_database_time_zones) |

## Dependencies

None. This role is self-contained.

## Example Playbook

```yaml
# Using default UTC timezone
- hosts: all
  roles:
    - role: configure_timezone

# Setting specific timezone
- hosts: europe_servers
  roles:
    - role: configure_timezone
      vars:
        timezone: "Europe/Paris"
```

## Usage in Other Roles

To include this role in another role's tasks:

```yaml
# Using default UTC timezone
- name: Configure timezone
  ansible.builtin.include_role:
    name: configure_timezone

# Setting specific timezone
- name: Configure timezone for region
  ansible.builtin.include_role:
    name: configure_timezone
  vars:
    timezone: "Asia/Tokyo"
```

## Supported Systems

This role supports:
- Regular Linux hosts
- LXC containers
- Both Debian and RedHat-based systems

## Implementation Details

The role performs the following actions:

1. For regular hosts:
   - Uses `community.general.timezone` module to set the timezone
   - Ensures `tzdata` package is installed
   - Updates `/etc/timezone`
   - Creates correct `/etc/localtime` symlink

2. For LXC containers:
   - Uses `timedatectl` to set the timezone
   - Ensures proper file configurations

3. For all systems:
   - Triggers appropriate handlers for tzdata updates
   - Validates timezone settings

## Tags

- `timezone`: All timezone-related tasks
- `configuration`: System configuration tasks

## License

MIT

## Author Information

Created for ProxmoxVE Ansible project 
