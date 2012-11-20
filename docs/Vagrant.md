# USING VAGRANT AND VIRTUALBOX WITH SHOESTRAP

Combining Vagrant, VirtualBox and Shoestrap creates a simple,
straightforward method for creating and provisioning development
and test systems on VirtualBox.

For more details on the individual componets, see:

    http://vagrantup.com
    http://virtualbox.org
    http://github.com/cmer/shoestrap

# Getting Started

## Installing Vagrant and VirtualBox

First, install Vagrant and VirtualBox. See the corresponding websites 
above for details.

## Installing Shoestrap

There is no 'installation' for Shoestrap. The data just gets put in a
local directory. The easiest way to do this is to just use git and
clone the Shoestrap repository:

    mkdir ~/git
    cd ~/git
    git clone https://githubcom/cmer/shoestrap.git

## Creating a Shoestrap Cookbook for a Test VM Guest

As an example, we will create a simple cookbook that does a package
update in the VM guest and adds git-core and vim.

    cd ~/git/shoestrap

    echo '#!/bin/bash'              >  vagrant-test
    echo '. helpers/initialize'     >> vagrant-test
    echo 'fail_if_not_root'         >> vagrant-test
    echo 'package_update'           >> vagrant-test
    echo 'package "git-core vim"'   >> vagrant-test
    echo 'finished'                 >> vagrant-test

Note: Vagrant already takes care of adding the user 'vagrant' and setting
up SSH for key-based login.

## Configuring Vagrant

The Vagrant side is pretty straightforward. Just add the desired image to
your set of available boxes and then create the Vagrant configuration file
for the instance we are starting. Note: you only need to add the box image
once. New instances will be cloned from this box.


    vagrant box add precious64 http://files.vagrantup.com/precise64.box
    mkdir ~/vagrant-test
    cd ~/vagrant-test
    vagrant init precise64

The 'vagrant init ...' command creates the file Vagrantfile in the current
directory. To add Shoestrap as the provisioning method for this configuration,
add the following lines to the 'Vagrant::Config.run do |config|' block of the
Vagrantfile:

    config.vm.share_folder "v-shoestrap", "/shoestrap", \
        "/Users/scott/git/shoestrap"
    config.vm.provision :shell, :inline => "cd /shoestrap && ./vagrant-test"
 
## Starting the Guest VM

Starting and using the Guest VM is as simple as:

    cd ~/vagrant-test
    vagrant up
    vagrant ssh


