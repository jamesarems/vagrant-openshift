# OKD 3.11 Vagrant setup for CentOS 7

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
