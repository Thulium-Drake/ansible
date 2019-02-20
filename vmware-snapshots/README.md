# Ansible VMWare playbooks
This set of playbooks will use the VMWare SDK to interact with a vCenter host:

* Retrieve inventory of VMs
* Create/Delete snapshots of VMs

I found that the VMWare inventory itself isn't particularly usefull as it does not take in account any folders or groups in vCenter.

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
pip install --upgrade --force-reinstall -r requirements.txt --extra-index-url "file:///$(pwd)/lib"
```
* Any version of Ansible 2.7 or up (That's what I've used to write this code)

Then, given that you either use the same names, or changed them in the inventory files (don't forget the host_vars!) you should be able to inventory the vCenter server.

I also created 2 folders: acc and prod, and 3 VMs: vm1, vm2 and vm3. All the VMs are listed seperately in the inventory, but can also be supplied by any other inventory source (like Foreman/Satellite).


