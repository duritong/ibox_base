# -*- mode: ruby -*-
# vi: set ft=ruby :


boxes = {
  'ibox-one'  => {
    #:ipaddress => '192.168.90.100',
    :cpus => 1,
    :memory => 1024,
    :disk2size => 1096,
  },
# 'ibox-two'  => {
#   #:ipaddress => '192.168.90.100',
#   :cpus => 1,
#   :memory => 1024,
#   #:disk2size => 1096,
# },
# 'ibox-three'  => {
#   #:ipaddress => '192.168.90.100',
#   :cpus => 1,
#   :memory => 1024,
#   #:disk2size => 1096,
#   #:os => 'centos6',
# },
}

options = {
  :root_password => 'vagrant',
  :domain        => 'ibox.local',
  :default_os    => 'centos7',
}

Vagrant.configure("2") do |config|
  boxes.each do |name,vopts|
    config.vm.define name do |vmconfig|
      os = vopts[:os] || options[:default_os]
      vmconfig.vm.box = "ibox-stemcell-#{os}-qemu"
      vmconfig.hostmanager.aliases = name

      vmconfig.vm.synced_folder '.', '/vagrant', disabled: true
      vmconfig.vm.synced_folder "manifests/", "/etc/puppet/ibox",
        type: 'rsync', rsync__exclude: ".git/"
      vmconfig.ssh.username = 'root'
      vmconfig.ssh.password = options[:root_password]
      vmconfig.vm.provision "shell", :inline => "/etc/puppet/ibox/bin/local_apply.sh"

      vmconfig.vm.hostname = "#{name}.#{options[:domain]}"
      if vopts[:ipaddress]
        vmconfig.vm.network :private_network, :ip => vopts[:ipaddress]
      end

      vmconfig.vm.provider "virtualbox" do |v, override|
        v.gui = true
        override.vm.box = "ibox-stemcell-#{os}-virtualbox"
        if vopts[:disk2size]
          disk2 = "./.vagrant/disk/#{name}_datadisk.vdi"
          v.customize ['createhd', '--filename', disk2, '--size',
            vopts[:disk2size]]
          v.customize ['storageattach', :id, '--storagectl', 'IDE Controller',
            '--port', 1, '--device', 1, '--type', 'hdd', '--medium', disk2]
        end
      end
    end
  end
end
