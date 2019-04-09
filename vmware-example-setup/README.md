# Ansible VMWare example setup
This folder contains an example on how to use Ansible with VMWare. It provides:

* VMWare inventory configuration
* Create/Delete snapshots of VMs in an example playbook that uses the
  [VMWare](https://galaxy.ansible.com/thulium_drake/vmware) role.

I found that the VMWare inventory itself isn't particularly usefull as it does
not take in account any folders or groups in vCenter. However, I've added it
so you can see how it should be configured. :-)

## Lab environment
For my setup I have used the following:

* KVM with ESXi installed: esxi.dom1.lab
* VM on the ESXi with vCenter appliance: vcenter.dom1.lab
* DNS server with both names so they will properly resolve

Get your vCenter trial licenses here: https://www.vmware.com/nl/try-vmware.html

Then on your Ansible control host, make sure you have the following installed:

* VMWare python SDK: https://code.vmware.com/web/sdk/65/vsphere-automation-python
```
git clone https://github.com/vmware/vsphere-automation-sdk-python
cd vsphere-automation-sdk-python
pip install --upgrade --force-reinstall -r requirements.txt --extra-index-url "file:////$(pwd)/lib"
```
* Any version of Ansible 2.7 or up (That's what I've used to write this code)

Then, given that you either use the same names, or changed them in the inventory
files (don't forget the group_vars!) you should be able to inventory the vCenter
server.

I also created 2 folders: acc and prod, and 3 VMs: vm1, vm2 and vm3. All the
VMs are listed seperately in the inventory, but can also be supplied by any
other inventory source (like Foreman/Satellite).

## Special note when using RHEL with SCL
If you are running a RHEL system for the API host, please note the following:

* RHEL does not come with PIP installable (at least not without EPEL)
* When installing something from the RHSCL, this is not active in your shell by default

In order to use this code, install pip as follows:

```
yum install python27-python-pip
```

Enable the environment:

```
scl enable python27 bash
```

Follow the installation instructions as above to setup PyVmomi and VMWare's SDK

Then change the ansible_python_interpreter for this host to the following script:

```
#!/bin/bash
. /opt/rh/python27/enable
exec python "$@"
```

All of the above actions are taken care of in the vmware role itself, however,
you still need to configure the ansible_python_interpreter, please check the
documentation of the role for more details.


# Multiple vSphere clusters? No problem!
The playbook is configured to lookup the vSphere credentials and information
in the hostvars of the targeted VM.  So, configuring for multiple VMware
clusters is as easy as defining these credentials for that specific host/group,
just like any other variable.

When Ansible runs the playbook, it will lookup all of the required information
for that specific targeted VM.
