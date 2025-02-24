# TPI Flash

Role to flash an OS to a TuringPI node, this role supports flashing a RK1 module or a Raspberry Compute Module 4.

## Requirements

- `tpi` command to be installed locally
- BMC SDCard: This role expects that a SDCard is present in the BMC, if there is no SDCard, you can flash remote by setting `flash.sdcard=false`
- hosts.yml: configuration options must be present

This role does requires certain specifics from the inventory configuration.

Each hosts MUST have the following configuration present.

| Variable | Type   | R/O      | Description                                                                                                                                    |
| -------- | ------ | -------- | ---------------------------------------------------------------------------------------------------------------------------------------------- |
| bmc      | string | Requried | Inventory hostname reference of the BMC where this node is located, this allows for multiple Turing PIs to be controlled by a single inventory |
| slot     | number | Required | Number of the TPI slot 1-4                                                                                                                     |
| type     | string | Required | Type of Node: Either RK1 or CM4; type NVIDIA is allowed to, however, this is not yet supported by this role                                    |
| root     | string | Optional | Root device where to move the root filesystem; example: `/dev/nvme0n1`                                                                         |

### Additional Variables

Additional variables which can be set through either group_vars, host_vars or the inventory.

| Variable | Type   | Description                                              |
| -------- | ------ | -------------------------------------------------------- |
| hostname | string | Hostname of node, will default to the inventory_hostname |

### Example hosts.yml (Single Turing PI)

**Please ensure the right type for each node**

```yaml
tpi:
  hosts:
    bmc: # Use this value in the node `bmc` key
      ansible_host: 192.168.10.15 # <-- Change this
      ansible_user: root

nodes:
  hosts:
    node1:
      bmc: bmc # <-- This references the host 'bmc' under group 'tpi'
      slot: 1
      type: RK1
      root: /dev/nvme0n1
    node2:
      bmc: bmc # <-- This references the host 'bmc' under group 'tpi'
      slot: 2
      type: RK1
      root: /dev/nvme0n1
    node3:
      bmc: bmc # <-- This references the host 'bmc' under group 'tpi'
      slot: 3
      type: RK1
      root: /dev/nvme0n1
    node4:
      bmc: bmc # <-- This references the host 'bmc' under group 'tpi'
      slot: 4
      type: CM4
```

### Example hosts.yml (Multiple Turing PIs)

```yaml
tpi:
  hosts:
    tpi1_rk1: # Turing PI #1 with only RK1 modules, use this value in the node `bmc` key
      ansible_host: 192.168.10.15 # <-- Change this
      ansible_user: root
    tpi2_cm4: # Turing PI #2 with only CM4 modules, use this value in the node `bmc` key
      ansible_host: 192.168.10.35 # <-- Change this
      ansible_user: root

nodes:
  hosts:
    node1:
      bmc: tpi1_rk1 # <-- This references the host 'tpi1_rk1' under group 'tpi'
      slot: 1
      type: RK1
      root: /dev/nvme0n1
    node2:
      bmc: tpi1_rk1 # <-- This references the host 'tpi1_rk1' under group 'tpi'
      slot: 2
      type: RK1
      root: /dev/nvme0n1
    node3:
      bmc: tpi1_rk1 # <-- This references the host 'tpi1_rk1' under group 'tpi'
      slot: 3
      type: RK1
      root: /dev/nvme0n1
    node4:
      bmc: tpi1_rk1 # <-- This references the host 'tpi1_rk1' under group 'tpi'
      slot: 4
      type: RK1
      root: /dev/nvme0n1
    node5:
      bmc: tpi2_cm4 # <-- This references the host 'tpi2_cm4' under group 'tpi'
      slot: 1
      type: CM4
    node6:
      bmc: tpi2_cm4 # <-- This references the host 'tpi2_cm4' under group 'tpi'
      slot: 2
      type: CM4
    node7:
      bmc: tpi2_cm4 # <-- This references the host 'tpi2_cm4' under group 'tpi'
      slot: 3
      type: CM4
    node8:
      bmc: tpi2_cm4 # <-- This references the host 'tpi2_cm4' under group 'tpi'
      slot: 4
      type: CM4
```

## Role Variables

All variables that can be controlled by the user start with the prefix `flash`, variables for this role are nested.

Example; flash a node use; `-e flash.node=true`

Example; force flash a node that is online and can be contacted use; `-e flash.node=true -e flash.force=true`

| Variable               | Description                                                                                                                                                                                  |
| ---------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| flash.sdcard           | Flash through SDCard, default: `true`, set to false if there is no BMC SDCard                                                                                                                |
| flash.node             | Flash node(s), in inventory or only the nodes, provided with the `-l` flag, this will flash all nodes that are off,offline,unreachable, if this flag is not provided NO node will be flashed |
| flash.force            | Force flash a node, will flash a node even if it is reachable                                                                                                                                |
| flash.version.rockchip | Override version of Rockchip for the RK1 image, currently: `2.4.0`                                                                                                                           |
| flash.version.rk1      | Provide different Ubuntu version for the RK1, dependent on `flash.version.rockchip`, current: `24.04`                                                                                        |
| flash.version.cm4      | Provide different Ubuntu version for the CM4, current: `24.04.1`                                                                                                                             |

## Example Playbook

Including an example of how to use your role (for instance, with variables passed in as parameters) is always nice for users too:

    - hosts: servers
      roles:
         - { role: username.rolename, x: 42 }

## License

MIT

## Author Information

G.J.R. Timmer, 

https://youtube.com/@TimmerTechIO

## Contributing

In order to contribute to this repository you can make changes to the inventory in the `tests` directory. Furthermore, a `ansible.cfg` file is required in the root of the project with the following content.

```ini
[defaults]
roles_path = ..
```

This will ensure you are able to test the role.

- Test Command: `ansible-playbook tests/test.yml -i tests/inventory.yml`
- Synatax Check: `ansible-playbook tests/test.yml -i tests/inventory.yml`
