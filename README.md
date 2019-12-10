## Requirements

This plugin requires the following modules:

- `libvirt`
- `xmltodict`

## Configuration

To use this inventory plugin, point your ansible inventory at a file named `libvirt.yml` that includes an appropriate `plugin:` line:

```
---
plugin: libvirt
```

Your `libvirt.yml` may include the following additional configuration options:

- `uri`: the libvirt connection URI. This is unset by default, which means it will use the libvirt default (`LIBVIRT_DEFAULT_URI` if is is set; otherwise `qemu:///session` for regular users, `qemu:///system` for root).

- `include_inactive`: by default, the plugin only reports on active guests. Set `include_inactive` to `true` to include all guests. Note that address information will not be available for inactive guests.

- `networks`: (list) when finding the address for a guest, only recognize addresses the are contained in one of the networks in this list.  If this is empty, report the first discovered address.

- `mechanisms`: (list) which mechanism to use to determine host addresses.  May be one or more of:

  - `agent` (use qemu guest agent)
  - `lease` (use information from the dhcp lease)
  - `arp` (use the system arp table)

  The default value is `['agent', 'lease', 'arp']`.

The `libviirt_inventory` plugin inherits from Ansible's "constructed" inventory plugin, so you can synthesize variables and groups from existing inventory information.  For example:

```
# Set ansible_user=root for all libvirt guests
compose:
  ansible_user: '"root"'

# Add guests with more than 64GB of ram to the large_memory group
groups:
  large_memory: libvirt.currentMemory['#text']|int > 64000000000
```
