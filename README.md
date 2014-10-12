# ibox - an immerda box

This is a vagrant box that represents the basis that all immerda boxes are built on. It is an easy way to get started with what looks like a VM within the immerda network.

## Requirements

* Have [Vagrant](http://vagrantup.com) running
* Install the following plugin

  vagrant plugin install vagrant-hostmanager

* If you want to use the kvm box install the libvirt vagrant

  vagrant plugin install vagrant-libvirt

## Setup

* Vagrant with kvm support. At the moment we relay on kvm support, other virtualization technologies might be supported in the future.
* Import the stemcell that is used to start as a basis

    # For virtualbox
    vagrant box add 'ibox-stemcell-centos7-virtualbox' 'https://ibox.immerda.ch/stemcells/virtualbox/ibox-centos7-x86_64-virtualbox.box'
    # For kvm/qemu
    vagrant box add 'ibox-stemcell-centos7-qemu' 'https://ibox.immerda.ch/stemcells/qemu/ibox-centos7-x86_64-qemu.box'

* Clone this repository and recursively checkout all the submodules

    git clone https://git.immerda.ch/ibox/ibox_base.git ibox
    cd ibox
    git submodule update --init --recursive

* Start the box

    # with your default provider
    SSH_AUTH_SOCK='' vagrant up
    # with a different provider
    SSH_AUTH_SOCK='' vagrant up --provider=libvirt
    # or
    SSH_AUTH_SOCK='' vagrant up --provider=virtualbox

* ssh to the box and apply the puppet manifests

    SSH_AUTH_SOCK='' vagrant ssh
    /etc/puppet/ibox/bin/local_apply.sh

## Tuning your deployment

The Vagrant file uses a simple yaml file: `boxes.yaml` to describe the boxes that should be created. You can use it to tweak the various options for a deployment, like an additional node, additional disk, cpu & memory. See `boxes.yaml.sample` for the various options you can tweak.

By default - if boxes.yaml is missing - only one box is started using all the defaults.

