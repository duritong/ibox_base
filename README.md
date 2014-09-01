# ibox - an immerda box

This is a vagrant box that represents the basis that all immerda boxes are built on. It is an easy way to get started with what looks like a VM within the immerda network.

## Setup

* Vagrant with kvm support. At the moment we relay on kvm support, other virtualization technologies might be supported in the future.
* Import the stemcell that is used to start as a basis

    vagrant box add 'ibox-stemcell' 'https://ibox.immerda.ch/stemcells/ibox-stemcell.box'

* Clone this repository and recursively checkout all the submodules

    git clone https://git.immerda.ch/ibox/ibox_base.git ibox
    cd ibox
    git submodule update --init --recursive

* Start the box

    SSH_AUTH_SOCK='' vagrant up

* ssh to the box and apply the puppet manifests

    SSH_AUTH_SOCK='' vagrant ssh
    /etc/puppet/ibox/bin/local_apply.sh



