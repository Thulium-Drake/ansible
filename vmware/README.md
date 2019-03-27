# VMWare role
This role provides multiple actions for use with VMWare clusters, it currently
features:

* Managing VM powerstates
* Managing VM snapshots

The most documentation can be found in the comment block at the top of each
file. And for an example setup in which you can use this, please check
[vmware-example-setup](../vmware-example-setup).

# Usage
This module requires the use of PyVmomi and the VMWare SDK, please check the
example setup for installation instructions.

## Powerstate
Supported states are:

 * Poweron a VM using either VMWare tools or the virtual power button
 * Poweroff a VM using either VMWare tools or the virtual power button
 * Reboot/reset a VM using either VMWare tools or the virtual power button

The role will NOT prompt for the required information, it can only be
provided using the playbook used to call it:

```
 - hosts: all
   gather_facts: no
   tasks:
   - set_fact:
       target_action: "powerstate"
       target_group: "{{ ansible_limit }}"
       target_state: "powered-on"
     delegate_to: localhost

   - import_role:
       name: vmware
     delegate_to: localhost
     run_once: yes
```

### Required variables:

 * target_action: one of the vmware_ playbooks that came with this role
 * target_group: a comma-separated list of hostgroups that are targeted
 * target_action: one of the states in the supported_states list

## Snap
Supported actions are:

 * Create snapshots (automatically with a datestamp, or with a provided name)
 * Delete given or all snapshots
 * Revert to a given snapshot, this script will NOT give you a list to pick from.

The role will NOT prompt for the required information, it can only be
provided using the playbook used to call it:

```
 - hosts: all
   gather_facts: no
   tasks:
   - set_fact:
       target_action: "snap"
       target_group: "{{ ansible_limit }}"
       target_state: "present"
     delegate_to: localhost

   - import_role:
       name: vmware
     delegate_to: localhost
     run_once: yes
```

### Required variables:

 * target_action: one of the vmware_ playbooks that came with this role
 * target_group: a comma-separated list of hostgroups that are targeted
 * target_state: one of the actions in the supported_states list

### Optional variables:

 * snapshot_name:
   o When creating snapshots: override the name of the snapshot
   o When reverting/deleting snapshots: the target snapshot to delete

