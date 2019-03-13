# Ansible VMWare playbooks
This set of playbooks will use the VMWare SDK to interact with a vCenter host:

* Retrieve inventory of VMs
* Create/Delete snapshots of VMs

I found that the VMWare inventory itself isn't particularly usefull as it does not take in account any folders or groups in vCenter. However, I've added it so you can see how it should be configured. :-)

## Lab environment
For my setup I have used the following:

* KVM with ESXi installed: esxi.dom1.carrier
* VM on the ESXi with vCenter appliance: vcenter.dom1.carrier
* DNS server with both names so they will properly resolve

Get your vCenter trial licenses here: https://www.vmware.com/nl/try-vmware.html

Then on your Ansible control host, make sure you have the following installed:

* VMWare python SDK: https://code.vmware.com/web/sdk/65/vsphere-automation-python (installation basically boiled down to the following):
```
git clone https://github.com/vmware/vsphere-automation-sdk-python
cd vsphere-automation-sdk-python
pip install --upgrade --force-reinstall -r requirements.txt --extra-index-url "file:////$(pwd)/lib"
```
* Any version of Ansible 2.7 or up (That's what I've used to write this code)

Then, given that you either use the same names, or changed them in the inventory files (don't forget the group_vars!) you should be able to inventory the vCenter server.

I also created 2 folders: acc and prod, and 3 VMs: vm1, vm2 and vm3. All the VMs are listed seperately in the inventory, but can also be supplied by any other inventory source (like Foreman/Satellite).

## Special note when using RHEL with SCL
If you are running a RHEL system for the 'vsphere_api_host', please note the following:

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
# This script enables the RedHat SCL with the correct modules for Python for use with Ansible
. /opt/rh/python27/enable

exec python "$@"
```


# Multiple vSphere clusters? No problem!
The playbook is configured to lookup the vSphere credentials and information in the hostvars of the targeted VM.
So, configuring for multiple VMware clusters is as easy as defining these credentials for that specific host/group, just like any other variable. For example:

* Default cluster for all machines -> group_vars/all.yml
```
---
# Connection information for vSphere
vpshere_api_host: 'localhost'                     # The host that has pyVmomi installed
vsphere_host: 'vcenter-prd.example.com'           # Your vCenter machine
vsphere_validate_certs: no                        # Whether to check vSphere's SSL cert or not
vsphere_username: 'administrator@vsphere.local'   # A useraccount with sufficient privileges
vsphere_password: 'Password1!'                    # The password for the above user
vsphere_datacenter: 'prod'                        # Which Datacenter your VM is in
```

* The cluster for your test VMs -> group_vars/ota.yml
```
---
# Connection information for vSphere
vpshere_api_host: 'localhost'                     # The host that has pyVmomi installed
vsphere_host: 'vcenter-ota.example.com'           # Your vCenter machine
vsphere_validate_certs: no                        # Whether to check vSphere's SSL cert or not
vsphere_username: 'administrator@vsphere.local'   # A useraccount with sufficient privileges
vsphere_password: 'Password1!'                    # The password for the above user
vsphere_datacenter: 'ota'                         # Which Datacenter your VM is in
```

* You can even make it work for that one super special machine -> host_vars/special.example.com.yml
```
---
# Connection information for vSphere
vpshere_api_host: 'ansible-special.example.com'   # The host that has pyVmomi installed
vsphere_host: 'vcenter-special.example.com'       # Your vCenter machine
vsphere_validate_certs: no                        # Whether to check vSphere's SSL cert or not
vsphere_username: 'administrator@vsphere.local'   # A useraccount with sufficient privileges
vsphere_password: 'Password1!'                    # The password for the above user
vsphere_datacenter: 'superspecial'                # Which Datacenter your VM is in
```

When Ansible runs the playbook, it will lookup all of the above information for that specific targeted VM.
