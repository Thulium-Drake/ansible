# Molecule installation playbook
This playbook will convert a debian system to a Ansible Molecule development/test system.

It will install the required packages to run Molecule and Ansible.

## Run in LXC
In my own setup I use an LXC container on a Proxmox system to develop my Ansible code. There's 2 variants to do so:

* Unprivileged LXC: This will run docker just fine, but you will not be able to develop roles that leverage systemd. As systemd requires some privileges
* Privileged LXC: This will run just about anything in docker ;-)

In order to run docker on a Proxmox LXC container (the only one I have configured, there's no reason why vanilla LXC does not work), make sure you do the following:

* Create a privileged container (for now only Debian support, I will try to make this playbook OS-agnostic later! :-) )
* Add the following lines to the containers config file:
```
features: keyctl=1,nesting=1
# Add these lines as well if you made the container privileged and wish to run systemd
lxc.cgroup.devices.allow: a
lxc.cap.drop:
```

# Setup
In order to set this up, do the following:

* Create a host (for instructions for LXC, see above)
* Make sure it has access to the Internet
* Add it to the hosts file
* Run Ansible
```
ansible-playbook molecule.yml
```
* Log in on the host and give docker a spin
```
docker run --privileged hello-world
```

You should see something like this:
```
Hello from Docker!
This message shows that your installation appears to be working correctly.

To generate this message, Docker took the following steps:
 1. The Docker client contacted the Docker daemon.
 2. The Docker daemon pulled the "hello-world" image from the Docker Hub.
    (amd64)
 3. The Docker daemon created a new container from that image which runs the
    executable that produces the output you are currently reading.
 4. The Docker daemon streamed that output to the Docker client, which sent it
    to your terminal.

To try something more ambitious, you can run an Ubuntu container with:
 $ docker run -it ubuntu bash

Share images, automate workflows, and more with a free Docker ID:
 https://hub.docker.com/

For more examples and ideas, visit:
 https://docs.docker.com/get-started/
```
