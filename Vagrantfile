# -*- mode: ruby -*-
# vi: set ft=ruby :


require 'yaml'

base_dir = File.dirname(File.expand_path(__FILE__))
if File.exists?(b=File.join(base_dir,'boxes.yaml'))
  boxes = YAML.load_file(b)
else
  boxes = YAML.load_file(File.join(base_dir,'boxes.yaml.sample'))
end
options = {
  'root_password'     => 'vagrant',
  'domain'            => 'ibox.local',
  'os'                => 'centos7',
  'private_networks'  => [],
  'gui'               => false,
}

Vagrant.configure("2") do |config|
  boxes.each do |name,vopts|
    opts = options.merge(vopts)
    config.vm.define name do |vmconfig|
      vmconfig.vm.box = "ibox-stemcell-#{opts['os']}-qemu"
      vmconfig.hostmanager.aliases = name

      vmconfig.vm.synced_folder '.', '/vagrant', disabled: true
      vmconfig.ssh.username = 'root'
      vmconfig.ssh.password = opts['root_password']

      vmconfig.vm.provision "shell", :inline => "/etc/puppet/ibox/bin/local_apply.sh"

      vmconfig.vm.hostname = "#{name}.#{opts['domain']}"

      vmconfig.vm.provider :virtualbox do |v,override|
        override.vm.box = "ibox-stemcell-#{opts['os']}-virtualbox"
        v.customize ["modifyvm", :id, "--cpus", opts['cpus']] if opts['cpus']
        v.customize ["modifyvm", :id, "--memory", opts['memory']] if opts['memory']

        # Add second disk for data/swap LVM volume
        if opts['disk2size']
          data_disk = File.join(base_dir,".vagrant/disk/#{name}_disk.vdi")
          v.customize ['createhd', '--filename', data_disk, '--size',
            opts['disk2size']] unless File.exists?(data_disk)
          v.customize ['storageattach', :id, '--storagectl', 'IDE Controller',
            '--port', 1, '--device', 1, '--type', 'hdd', '--medium', data_disk]
        end

        opts['private_networks'].each_with_index do |ho_net_ip,i|
          override.vm.network "private_network", ip: ho_net_ip, virtualbox__intnet: true, intnet: "intnet#{i}"
        end
        opts['ports'].each do |g,h|
          config.vm.network "forwarded_port", guest: g, host: h
        end
        override.vm.synced_folder "manifests/", "/etc/puppet/ibox"

        v.gui = opts['gui']
      end
      vmconfig.vm.provider :libvirt do |v,override|
        override.vm.synced_folder "manifests/", "/etc/puppet/ibox",
          type: 'rsync', rsync__exclude: ".git/"
      end
    end
  end
end
