# -*- mode: ruby -*-
# vi: set ft=ruby :

Vagrant.configure("2") do |config|
  config.vm.box = "ibox-stemcell"
  config.vm.synced_folder '.', '/vagrant', disabled: true
  config.vm.synced_folder "manifests/", "/etc/puppet/ibox",
    type: 'rsync', rsync__exclude: ".git/"
  config.ssh.username = 'root'
  config.ssh.password = 'vagrant'
  config.vm.provision "shell", :inline => "/etc/puppet/ibox/bin/local_apply.sh"
end
