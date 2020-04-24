# OKD 3.11 & Rook Vagrant setup for CentOS 7 

### Setup's are,

- 1 master and 2 worker nodes
- OS CentOS 7.x
- Additional disk for rook Ceph
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

### Rook Configuration

- open `rook` directory
- Deploy manifest step by step

> oc create -f common.yaml
<br/>
> oc create -f operator-openshift.yaml
<br/>
> oc create -f cluster.yaml
<br/>
> oc create -f storageclass.yaml
<br/>
> oc create -f pvc.yaml
<br/>
> oc create -f pod.yaml
