---
layout: post
title: "Converting OVA images to KVM"
description: "Converting an Open Virtual Appliance OVA-File to use in KVM"
category: server
tags: [Linux, Ubuntu, Debian, OVA, Virtual Appliance]
---
{% include JB/setup %}

The Open Virtualization Format (OVF) is an open standard for packaging and distributing virtual appliances for the use in virtual machines. 
An OVA file is a one file alternative to pack a multi file OVF directory. 
This is a short example how to convert OVA for the use in a proxmox 2.0 KVM environment. The OVA file was exported from the Virtualbox software, where we had build a debian based virtual machine. 

## Extract the OVA Archive
OVA files are TAR archives with the OVF directory inside. <br />
To extract the archive do 
 
    tar -xvf Virtual_Appliance_Debian.ova
    => 
    Virtual_Appliance_Debian.ovf
    Virtual_Appliance_Debian-disk.vmdk

You get Virtual_Appliance_Debian-disk.vmdk which is a virtual machine disk file. The VMDK file format was developed for the use in VMWare or Virtualbox. It is an open format.

## Converting VMDK to RAW and from RAW to qcow2
With the free available software Virtualbox it is possible to convert VMDK disk files to RAW image format. 

    VBoxManage clonehd --format RAW Virtual_Appliance_Debian-disk.vmdk Virtual_Appliance_Debian-disk.raw
    => Virtual_Appliance_Debian-disk.raw
    
Convert the RAW image file to qemu format: 

    qemu-img convert -f raw Virtual_Appliance_Debian-disk.raw -O qcow2  Virtual_Appliance_Debian-disk.qcow2
    => Virtual_Appliance_Debian-disk.qcow2
<br />
## Use qcow2 files in Proxmox 2.0
The use of an existing qcow2 file in proxmox 2.0 is very easy. Just create a new virtual machine and replace the disk file with the qcow2 file. 

* create a new KVM virtual machine via the proxmox webinterface.
  * Click __Create VM__.
  * __General__: select Node, VM ID and Name.
  * __OS__: Select the operating system type of your virtual machine.
  * __CD/DVD__: Do not use any media.
  * __Hard Disk__: Select existing lvm storage for __storage__ and choose same or better bigger disk size as the original VM disk size.
  * __other Tabs__: as needed for the new virtual machine.
  * __Confirm__: Click __Finish__

Proxmox has created a new disk file for the virtual machine. Do not start the new VM. Replace the proxmox generated qcow2 file with our Virtual_Appliance_Debian-disk.qcow2    
 
    mv Virtual_Appliance_Debian-disk.qcow2 /var/lib/vz/images/105/vm-105-disk-1.qcow2
   
Start the virtual machine via the proxmox webinterface.

## Insertion: About Unzipping 7zip files
Sometimes we use 7zip compressed files for easier transportation of disk images. <br /> 
Install 7zip if not already installed:

    sudo apt-get install p7zip

Unzip the 7zip file:

    7z x Virtual_Appliance_Debian.ova.7z
    
<br />
## see also
external information sources:
* Convert Virtualbox vdi to KVM qcow: [http://blog.bodhizazen.net/linux/convert-virtualbox-vdi-to-kvm-qcow/](http://blog.bodhizazen.net/linux/convert-virtualbox-vdi-to-kvm-qcow/)
* Migration of servers to Proxmox VE: [http://pve.proxmox.com/wiki/Migration_of_servers_to_Proxmox_VE#VMware_to_Proxmox_VE_.28KVM.29](http://pve.proxmox.com/wiki/Migration_of_servers_to_Proxmox_VE#VMware_to_Proxmox_VE_.28KVM.29)
* qcow image format: [http://people.gnome.org/~markmc/qcow-image-format.html](http://people.gnome.org/~markmc/qcow-image-format.html)
     
