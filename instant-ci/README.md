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

## SSL Certificate
The role in this lab will automagically create a SSL certificate for the configured domain.

If you want to use your own (wildcard!) certificate, do the following:

* Set ```traefik_selfsign``` to False
* Make sure you provide ```{{ docker_data_dir }}/traefik_data/traefik.[crt|key]```

The rest of the setup will then work just fine!

# Setup
In order to set this up, do the following:

* Create a DNS wildcard record for a subdomain:
 *.dckr.example.com to the Docker Swarm master
* Create a couple of hosts (physical or virtual)
* Make sure they have access to the Internet
* Add them to the hosts
* Install all requirements
```
ansible-galaxy install -r requirements.yml
```
* Copy the defaults file from roles/docker_lab/defaults to group_vars/all.yml
  * If you use gluster as well, add the values from roles/gluster_volume/defaults as well!
```
cat roles/gluster_volume/defaults/main.yml >> group_vars/all.yml
```
  * Add the following lines to group_vars/all.yml
```
pip_install_packages:
    - name: 'docker'
```
* Run Ansible
```
ansible-playbook main.yml
```

# Usage
When Ansible is done with the setup, you should have access to the following URLs:

* Portainer: https://portainer.dckr.example.com
* Gitea: https://gitea.dckr.example.com
* Drone: https://drone.dckr.example.com (needs additional setup, see below)
* Gitea's SSH server is reachable on port 2222

In order to use these, you need to do the following things:

* Create your administrative useraccounts for Portainer and Gitea
  * Drone will leverage the Oauth2 provider in Gitea
* In Gitea, go to 'Settings -> Applications'
* Create a new Oauth2 application
  * Name: Drone
  * Redirect URL: https://drone.dckr.example.com/login
* Put the Client ID and the secret in your group_vars file for the lab host
* Redeploy Drone
* Go to the Drone webinterface, you will be redirected to Gitea.
* Authorize the request from Drone to use Oauth
* You can now use the Drone interface without extra logins!

Alternatively, if you modify ```roles/docker_lab/vars/main.yml``` you can also
run Drone without SSO. Change ```drone``` to ```drone_no_oauth2```


# Destroy lab
To remove the lab in it's entierity, run the ```remove.yml``` playbook.

```
ansible-playbook remove.yml
```
