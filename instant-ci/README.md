# Instant CI Lab, just add docker
This Ansible project will create a small lab with the following parts:

* Drone: as the CI/CD controller
* Gitea: to use as SCM
* MariaDB: for all the SQL requirements
* Portainer: a Docker web GUI to keep track of things
* Traefik: a docker-integrated reverse Proxy to expose all the webinterfaces

All of this will be configured on a docker swarm and this swarm can be extended
with all hosts in the ```[workers]``` group.

## Shared data
All shared data will be placed on a gluster filesystem, this has been configured
as such all data is replicated to all servers in the cluster.

# Setup
In order to set this up, do the following:

* Create a couple of hosts (physical or virtual)
* Make sure they have access to the Internet
* Add them to the hosts file
* Install all requirements
```
ansible-galaxy install -r requirements.yml
```
* Run Ansible
```
ansible-playbook main.yml
```
