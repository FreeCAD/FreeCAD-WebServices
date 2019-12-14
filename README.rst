Introduction
------------

This repository contains an infrastructure-as-code representation of FreeCAD's
web services. Using Vagrant as a provisioning frontend to LXD containers and
Libvirt VMs configured by Ansible playbooks, one can* stand up any or all of the
services for a production or development deployment. Eventually, sample DBs may
be included for development, such as a phpBB backup with the correct FreeCAD
forums but no posts or user data. While the repo and instructions target recent
Debian/Ubuntu systems, pull requests are welcome to expand that, as well as
support for Docker, Vagrant + VirtualBox or the like.

The goal of this repo is to make maintenance & development of our web service
infrastructure easier.

The web services currently in use are:

* Forum - phpBB
* Homepage - static HTML+JS+CSS
* Issue Tracker - MantisBT
* Wiki - MediaWiki

with a backing MySQL database.

Future experimental web services may be included. Vagrant also supports cloud
deployment targets such as AWS or Digital Ocean, so in the future it may be
possible to use this repo to directly deploy and manage our infrastructure.

\* eventually

Requirements & Installation
---------------------------

* `Ansible <https://www.ansible.com/>`_
* `Libvirt <https://libvirt.org/>`_
* `LXD <https://linuxcontainers.org/lxd/>`_
* `Vagrant <https://www.vagrantup.com/>`_ + `vagrant-libvirt
  <https://github.com/vagrant-libvirt/vagrant-libvirt>`_ & `vagrant-lxd
  <https://gitlab.com/catalyst-it/devtools/vagrant-lxd>`_ plugins

For a Debian/Ubuntu system::

    $ wget https://releases.hashicorp.com/vagrant/2.2.6/vagrant_2.2.6_x86_64.deb
    $ sudo apt install ./vagrant_2.2.6_x86_64.deb
    $ sudo apt install ansible libvirt-daemon-system libvirt-dev ruby-dev snapd btrfs-progs
    $ sudo snap install lxd
    $ sudo /snap/bin/lxd init --auto
    $ vagrant plugin install vagrant-libvirt
    $ vagrant plugin install vagrant-lxd
    $ sudo usermod -aG libvirt,lxd $(whoami)
    
Then reboot.

If you are not using LXD, installation becomes much simpler. This is because
Vagrant in the Debian/Ubuntu repositories is patched in order to work with
``apt``-packaged plugins , which breaks ``vagrant plugin install`` ing from the
Internet, which is needed for ``vagrant-lxd``, which in turn requires we
download ``vagrant`` directly from upstream. So, instead you can do::

    $ sudo apt install vagrant vagrant-libvirt libvirt-daemon-system
    $ sudo usermod -aG libvirt $(whoami)
    $ newgrp libvirt

without restarting.

See additional provider-specific documentation: `<lxd/README.rst>`_.

Usage
-----

Clone the repository::

    $ git clone https://github.com/freecad/freecad-webservices
    $ cd freecad-webservices   

Stand up a single service or all at once::

    $ cd lxd  # or cd libvirt
    $ vagrant up forum
    $ vagrant up  # stands up forum, tracker, wiki, homepage

Test Ansible provisioning or get shell access for development::

    $ vagrant provision wiki
    $ vagrant ssh wiki

Clean up afterward::

    $ vagrant halt forum  # free up resources but don't erase
    $ vagrant destroy -f wiki  # destroys without confirmation
