# -*- mode: ruby -*-
# vi: set ft=ruby :

#Hostnames
$master_host = "okd-master.otg.ae"
$node1_host = "okd-node1.otg.ae"
$node2_host = "okd-node2.otg.ae"
$node3_host = "okd-node3.otg.ae"

#Mac address
$master_mac = "8e:94:5b:64:4a:14"
$node1_mac = "8e:94:5b:64:4a:15"
$node2_mac = "8e:94:5b:64:4a:16"
$node3_mac = "8e:94:5b:64:4a:17"

#Host script
$host_script = <<-SHELL
      echo "[>>>>>>>>>>>>>>>>] Disable SELINUX"
      setenforce 0
      sed -i s/^SELINUX=.*$/SELINUX=disabled/ /etc/sysconfig/selinux
      echo "[>>>>>>>>>>>>>>>>] Updating packages"
      yum update -y > /dev/null 2>&1
      echo "[>>>>>>>>>>>>>>>>] Disable Swap"
      swapoff -a
      echo "[>>>>>>>>>>>>>>>>] Enable Firewalld"
      systemctl unmask --now firewalld
      systemctl enable --now firewalld
      echo "[>>>>>>>>>>>>>>>>] Installing Dependencies"
      yum install wget git net-tools vim bind-utils yum-utils iptables-services bridge-utils bash-completion kexec-tools sos psacct docker-1.13.1 -y > /dev/null 2>&1
      yum -y install centos-release-openshift-origin311 epel-release git pyOpenSSL > /dev/null 2>&1
      sed -i -e "s/^enabled=1/enabled=0/" /etc/yum.repos.d/epel.repo
      echo "[>>>>>>>>>>>>>>>>] Adding hosts file"
      echo "127.0.0.1   localhost localhost.localdomain localhost4 localhost4.localdomain4" > /etc/hosts
      echo "::1         localhost localhost.localdomain localhost6 localhost6.localdomain6" >> /etc/hosts
      echo "192.168.99.51	okd-master.otg.ae	okd-master" >> /etc/hosts
      echo "192.168.99.52	okd-node1.otg.ae	okd-node1" >> /etc/hosts
      echo "192.168.99.53	okd-node2.otg.ae	okd-node2" >> /etc/hosts
      echo "192.168.99.54	okd-node3.otg.ae	okd-node3" >> /etc/hosts
      echo "[>>>>>>>>>>>>>>>>] Create user Origin and its sudo file"
      useradd origin
      echo "origin" | passwd --stdin origin
      echo -e 'Defaults:origin !requiretty\norigin ALL = (root) NOPASSWD:ALL' | tee /etc/sudoers.d/openshift 
      chmod 440 /etc/sudoers.d/openshift
      echo "[>>>>>>>>>>>>>>>>] Starting Docker"
      systemctl enable --now docker
      echo "[>>>>>>>>>>>>>>>>] Patching SSH"
      mkdir /home/origin/.ssh
      chmod 700 /home/origin/.ssh
      cp -r /vagrant/authorized_keys /home/origin/.ssh/
      chmod 600 /home/origin/.ssh/authorized_keys
      chown -R origin:origin /home/origin/.ssh
      sed -i 's/PasswordAuthentication no/PasswordAuthentication yes/g' /etc/ssh/sshd_config
      systemctl restart sshd
      echo "[>>>>>>>>>>>>>>>>] Machine is ready for Openshift installation"
SHELL

#Master script
$master_script = <<-SHELL
      echo "[>>>>>>>>>>>>>>>> MASTER <<<<<<<<<<<<<<] OKD Ansible"
      yum -y install openshift-ansible > /dev/null 2>&1
      cp /vagrant/config /home/origin/.ssh/config
      chmod 600 /home/origin/.ssh/config
      ssh-keyscan -t ecdsa-sha2-nistp256 okd-master.otg.ae >> /home/origin/.ssh/known_hosts
      ssh-keyscan -t ecdsa-sha2-nistp256 okd-node1.otg.ae >> /home/origin/.ssh/known_hosts
      ssh-keyscan -t ecdsa-sha2-nistp256 okd-node2.otg.ae >> /home/origin/.ssh/known_hosts
      ssh-keyscan -t ecdsa-sha2-nistp256 okd-node3.otg.ae >> /home/origin/.ssh/known_hosts
      chmod 644 /home/origin/.ssh/known_hosts
      cp -r /vagrant/id_rsa* /home/origin/.ssh/
      chmod 600 /home/origin/.ssh/id_rsa
      chmod 644 /home/origin/.ssh/id_rsa.pub
      chown -R origin:origin /home/origin/.ssh
      rm -rf /etc/ansible/hosts
      cp /vagrant/hosts /etc/ansible/hosts
      su - origin -c "ansible-playbook /usr/share/ansible/openshift-ansible/playbooks/prerequisites.yml"
      sleep 3s
      su - origin -c "ansible-playbook /usr/share/ansible/openshift-ansible/playbooks/deploy_cluster.yml"
      echo "[>>>>>>>>>>>>>>>>] Creating admin user"
      sleep 3s
      su - origin -c "sudo htpasswd -b -c /etc/origin/master/htpasswd admin admin"
      su - origin -c "oc adm policy add-cluster-role-to-user cluster-admin admin"
      echo "User admin has been created with cluster-admin privillege."
      echo "[>>>>>>>>>>>>>>>>] Installation completed"
      echo "----------------------------"
      
SHELL

Vagrant.configure("2") do |config|

  config.vm.define :okd_master do |okd_master|
    okd_master.vm.box = "centos/7"
    okd_master.vm.hostname = $master_host
    #test_vm.vm.network :private_network, :ip => '10.20.30.40'
    okd_master.vm.network :public_network, :mac => $master_mac, :dev => "br0", :mode => "bridge", :type => "bridge"
    okd_master.vm.provider :libvirt do |domain|
      domain.memory = 16384
      domain.cpus = 6
    end
    okd_master.vm.provision "shell", inline: $host_script
    okd_master.vm.provision "shell", inline: $master_script
    okd_master.vm.synced_folder './config', '/vagrant', type: 'rsync'
  end

  config.vm.define :okd_node1 do |okd_node1|
    okd_node1.vm.box = "centos/7"
    okd_node1.vm.hostname = $node1_host
    #test_vm.vm.network :private_network, :ip => '10.20.30.40'
    okd_node1.vm.network :public_network, :mac => $node1_mac, :dev => "br0", :mode => "bridge", :type => "bridge"
    okd_node1.vm.provider :libvirt do |domain|
      domain.memory = 16384
      domain.cpus = 6
      domain.storage :file, 
        :size => '50G'
    end
    okd_node1.vm.provision "shell", inline: $host_script
    okd_node1.vm.synced_folder './config', '/vagrant', type: 'rsync'
  end

  config.vm.define :okd_node2 do |okd_node2|
    okd_node2.vm.box = "centos/7"
    okd_node2.vm.hostname = $node2_host
    #test_vm.vm.network :private_network, :ip => '10.20.30.40'
    okd_node2.vm.network :public_network, :mac => $node2_mac, :dev => "br0", :mode => "bridge", :type => "bridge"
    okd_node2.vm.provider :libvirt do |domain|
      domain.memory = 16384
      domain.cpus = 6
      domain.storage :file, 
        :size => '50G'
    end
    okd_node2.vm.provision "shell", inline: $host_script
    okd_node2.vm.synced_folder './config', '/vagrant', type: 'rsync'
  end
  
  config.vm.define :okd_node3 do |okd_node3|
    okd_node3.vm.box = "centos/7"
    okd_node3.vm.hostname = $node3_host
    #test_vm.vm.network :private_network, :ip => '10.20.30.40'
    okd_node3.vm.network :public_network, :mac => $node3_mac, :dev => "br0", :mode => "bridge", :type => "bridge"
    okd_node3.vm.provider :libvirt do |domain|
      domain.memory = 16384
      domain.cpus = 6
      domain.storage :file, 
        :size => '50G'
    end
    okd_node3.vm.provision "shell", inline: $host_script
    okd_node3.vm.synced_folder './config', '/vagrant', type: 'rsync'
  end

  config.vm.provider :libvirt do |libvirt|
    libvirt.driver = "kvm"
    libvirt.host = "192.168.99.50"
    libvirt.qemu_use_session = false
    libvirt.connect_via_ssh = true
    libvirt.username = "root"
    libvirt.password = "it@otg"
    libvirt.storage_pool_name = "default"
  end

end


