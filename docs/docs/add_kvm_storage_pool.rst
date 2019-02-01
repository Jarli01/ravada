Add KVM storage pool
====================

If you run out of disk space you may add a new disk. 

.. note :: 
    KVM must then be informed about this new space available by creating a new storage pool.

Add the drive to the system
---------------------------

After booting with the new drive, check dmesg to find out the name of
the new disk. It will probably be called /dev/sdSOMETHING.

Double check this is actually the new disk, if not you may erase all the
contents of the system. Type *df* to see the old disk partitions.

Create a new partition with *fdisk*. It should show it as empty. Add
only one primary partition for all the free space.

Replace *sdX* by the real name of the new device:

.. prompt:: bash $

    sudo fdisk /dev/sdX

Format it with large files tunning:

.. prompt:: bash $

    sudo mkfs.ext4 -m 0.001 -T largefiles /dev/sdX1

Mount the new partition
-----------------------

Add this new partition to the filesystem table:

.. prompt:: bash $,$,(env)...$ auto

    sudo mkdir /var/lib/libvirt/images.2
    sudo vim /etc/fstab
    /dev/sdb1   /var/lib/libvirt/images.2 ext4  auto    0   3

It will mount it next time you boot, but it can be used without
rebooting issuing:

.. prompt:: bash $

    sudo mount -a

Add the drive to the Virtual Manager
------------------------------------

.. prompt:: bash $

    sudo virsh pool-define-as pool2 dir - - - - /var/lib/libvirt/images.2
    sudo virsh pool-autostart pool2
    sudo virsh pool-start pool2
    sudo virsh pool-list
 

And that's it, now Ravada will use the pool that has more empty space
the next time it needs to create a volume. If you want to fine tune
what storage pool is used by default follow the
`advanced settings documentation <advanced_settings.html>`__ .
