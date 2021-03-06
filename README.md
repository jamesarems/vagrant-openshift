# OKD 3.11 Vagrant setup for CentOS 7

### Branch 

- master - Openshift installation and configurations only
- rook - Openshift installation with `rook` support

### Setup's are,

- 1 master and 2 worker nodes
- OS CentOS 7.x
- Vagrant
- Libvirt (kvm)


### Security advice

If you clone this, use this `config` directory files for testing only.
For production or other security installation , update with your own ssh keys.

### Config advice

- `config/hosts` contains ansible configurations. Change hostname or IP address according to your network.
- Edit `Vagrantfile` according to your needs.
- To get `route` application hostname you should create a wildcard DNS entry.

### After installation

- `vagrant ssh okd_master` and login as `origin` user with password `origin`. (You can chnage the password from `Vagrantfile`)
- List all nodes by `oc get no`
- By default `htpassword` is the authentication method. After installation you should create a user by `sudo htpasswd /etc/origin/master/htpasswd admin`
- Give this user cluster admin previllege by `oc adm policy add-cluster-role-to-user cluster-admin admin`
- Login to your OKD by `oc login -u admin https://<okdmaster-host>:8443` or Using web browser `https://<okdmaster-host>:8443`
