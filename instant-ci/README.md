# Instant CI Lab, just add docker
This Ansible project will create a small lab with the following parts:

* Drone: as the CI/CD controller
* Gitea: to use as SCM
* MariaDB: for all the SQL requirements
* Portainer: a Docker web GUI to keep track of things
* Traefik: a docker-integrated reverse Proxy to expose all the webinterfaces

All of this will be configured on a docker swarm and this swarm can be extended
with all hosts in the ```[workers]``` group.

Please note that I have only tested this setup with 1 manager node in the swarm!

## Shared data
All shared data will be placed on a gluster filesystem, this has been configured
as such all data is replicated to all servers in the cluster.

## Security
Because I am new to Docker, it is very possible I missed a few things with regards
to securing this little toy. The things I DID do:

* All apps have their own network and only share networks if they need to talk
with each other.
* If an app needs a database, it has it's own database instance.
* All webinterfaces have been put behind Traefik and are secured using self-signed
certificates.

Things to take note of (that I can think of):
* The Docker Swarm does not use TLS certificates (just TLS without verification)
* Gluster does not use SSL
* The SQL servers will not dump their databases on a regular basis! You'd need
to add a container in the $APP_backend network with access to a disk and a MySQL client

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

# Usage
When Ansible is done with the setup, you should have access to the following URLs:

* Portainer: https://portainer.example.com
* Gitea: https://gitea.example.com
* Drone: https://drone.example.com

In order to use these, you need to do the following things:

* Create your administrative useraccounts for Portainer and Gitea
  * Drone will use the credentials you use for Gitea
* Finish the setup of Gitea (just click Install), all information is already filled in!
