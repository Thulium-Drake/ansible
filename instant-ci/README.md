# Instant CI Lab, just add docker
This Ansible project will create a small lab with the following parts:

* Gitea: to use as SCM
* Drone: as the CI/CD controller
* Portainer: a Docker web GUI to keep track of things
* CockroachDB: to make sure your data survives nuclear fallout ;-)
* Traefik: a docker-integrated reverse Proxy to expose all the webinterfaces

All of this will be configured on a docker swarm and this swarm can be extended
with all hosts in the ```[workers]``` group

# Setup
In order to set this up, do the following:

* Create a couple of hosts (physical or virtual)
* Make sure they have access to the Internet
* Add them to the hosts file
* Run Ansible
```
ansible-playbook main.yml
```
