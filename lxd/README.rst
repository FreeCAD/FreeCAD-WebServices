LXD Notes
---------

In the main README, LXD is configured with ``sudo /snap/bin/lxd init --auto``
after installing ``btrfs-progs``, which creates a storage pool using btrfs, one
of the `recommended LXD storage pool backends
<https://lxd.readthedocs.io/en/latest/storage/#feature-comparison>`_.

If the ``--auto`` option is omitted, you can select a different storage backend
such as ZFS, LVM, or plain directory backing.

After ``lxd init``, there are a few additional steps required:

*  Configure LXD to listen on HTTPS::
  
     $ lxc config set core.https_address 127.0.0.1
     $ lxc config trust add ~/snap/lxd/*/.config/lxc/client.crt

* Add shared folder support as documented in ``vagrant lxd shadow --help``::

     $ echo root:1000:1 | sudo tee -a /etc/subuid
     $ echo root:1000:1 | sudo tee -a /etc/subgid
