# -*- mode: ruby -*-
# vi: set ft=ruby :

# The number of labvms to provision
$num_labvm = (ENV['NUM_LABVMS'] || 1).to_i

# ip configuration
$labvm_ip_base = ENV['LABVM_IP_BASE'] || "192.168.100.2"
$labvm_ips = $num_labvm.times.collect { |n| $labvm_ip_base + "#{n+3}" }
#$num_labvm.times do |n|
#  print $labvm_ips[n] 
#end

Vagrant.configure(2) do |config|
  config.vm.box = "centos/7"
  config.vm.synced_folder ".", "/vagrant", disabled: true
  config.ssh.private_key_path = "~/.vagrant.d/insecure_private_key"
  config.ssh.insert_key = false
#  config.ssh.password = "vagrant"
  $num_labvm.times do |n|
    config.vm.define "labvm-#{n+1}" do |labvm|
      labvm_index = n+1
      labvm_ip = $labvm_ips[n]
      labvm.vm.network "private_network", ip: "#{labvm_ip}"
      labvm.vm.hostname = "lab-vm-#{labvm_index}"
      config.vm.provision "shell", inline: <<-SHELL
      sudo -s

      echo "[rockstor]" >  /etc/yum.repos.d/rockstor.repo
      echo "name=Rockstor 3 - x86_64" >> /etc/yum.repos.d/rockstor.repo
      echo "baseurl=http://rockstor.com/rockrepo/" >> /etc/yum.repos.d/rockstor.repo
      echo "enabled=1"  >> /etc/yum.repos.d/rockstor.repo
      echo "gpgcheck=0"  >> /etc/yum.repos.d/rockstor.repo
      echo "metadata_expire=1m"  >> /etc/yum.repos.d/rockstor.repo

      yum install https://dl.fedoraproject.org/pub/epel/epel-release-latest-7.noarch.rpm -y 
      yum install git czmq-devel -y 
      yum groupinstall "Development Tools" -y
      git clone https://github.com/rockstor/rockstor-core.git 
      cd rockstor-core
      python bootstrap.py -c buildout.cfg
      ./bin/buildout -N -c buildout.cfg
      SHELL
    end
  end
end
