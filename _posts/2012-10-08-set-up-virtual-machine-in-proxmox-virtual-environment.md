---
layout: post
title: "Set Up Virtual Machine in Proxmox Virtual Environment"
description: ""
category: 
tags: []
---
{% include JB/setup %}


![Proxmox Virtual Environment](/images/fw_dmz.png)

##Scenario


* Physical Server with public IP
  *  Proxmox 2.0 (virtualization management solution for servers)
  *  Shorewall (gateway/firewall configuration tool for GNU/Linux)
  *  Apache 2 Webserver
* Virtual Machine in DMZ (Demilitarized Zone behind firewall)
  *  local network (IPs in Range of 10.0.0.X, 192.168.X.X, ...)
  *  full virtualization with KVM

<br />
##Shorewall Setup

Set ssh login for virtual machines to a different port and forward it to virtual machine (VM) port 22

in `/etc/shorewall/rules`

    # ssh access
    DNAT            net             dmz:10.0.0.100:22       tcp     100
    DNAT            net             dmz:10.0.0.101:22       tcp     101
    DNAT            net             dmz:10.0.0.102:22       tcp     102
    DNAT            net             dmz:10.0.0.103:22       tcp     103
    # and so on...
    
ssh login is `ssh user@SERVERIP -p 10X` . Restart shorewall after changes with `shorewall restart &` 

dmz and network is defined in `/etc/shorewall/interfaces`

    #ZONE   INTERFACE       BROADCAST       OPTIONS
    net     eth0            detect          blacklist,nosmurfs
    dmz     venet0          detect          routeback
    dmz     vmbr0           detect          routeback,bridge


##Apache 2 Setup

Let's supose to setup a website mysite.net at the virtual machine 10.0.0.101. Edit `/etc/apache2/sites-available/mysite` 

    <VirtualHost *:80>
        ProxyPreserveHost On
        #ProxyVia Block
        ProxyPass / http://10.0.0.101/
        ProxyPassReverse / http://10.0.0.101/
        TransferLog /var/log/apache2/mysite.access
        ErrorLog    /var/log/apache2/mysite.error
        ServerName mysite.net
        ServerAlias www.mysite.net
    </VirtualHost>

enable the site with `a2ensite mysite` and reload apache e.G.: `/etc/init.d/apache2 reload`.

Or in a more complex setup one can route specific directories to different webservices on the virtual machine.

    <VirtualHost *:80>
        ProxyPreserveHost On
        #ProxyVia Block
        ProxyPass /task http://10.0.0.101:8086/task
        ProxyPassReverse /task http://10.0.0.101:8086/task
        ProxyPass /investigation http://10.0.0.101:8087/investigation
        ProxyPassReverse /investigation http://10.0.0.101:8087/investigation
        TransferLog /var/log/apache2/mysite.access
        ErrorLog    /var/log/apache2/mysite.error
        ServerName mysite.net
        ServerAlias www.mysite.net
    </VirtualHost> 

<br />

## Proxmox Setup

### Create VM from a snapshot
If one have a snapshot of a ready debian machine do:

* create a new KVM virtual machine at the proxmox webinterface.
  * Click __Create VM__.
  * __General__: select Node, VM ID and Name.
  * __OS__: Select the operating system type of your virtual machine.
  * __CD/DVD__: Do not use any media.
  * __Hard Disk__: Select existing lvm storage for __storage__ and choose same or better bigger disk size as the original VM disk size.
  * __other Tabs__: as needed for the new virtual machine.
  * __Confirm__: Click __Finish__

Proxmox has created a new VM. __Do not start it__. Restore your snapshot MyReadyDebian-disk.tar to the new VM.     
 
    qmrestore MyReadyDebian-disk.tar 10X -force 

__IMPORTANT__ change the MAC address of the network device. Or remove the network device and add a new one (this will also create a new MAC address).  

Start the virtual machine via the proxmox webinterface.

### Setup VM and networking with the Proxmox console
* `/etc/hostname` change the name to servername (myserver)
* `/etc/network/interfaces` configure network
<pre><code># The primary network interface
    allow-hotplug eth0
    iface eth0 inet static
        address 10.0.0.10X
        netmask 255.255.255.0
        network 10.0.0.0
        broadcast 10.0.0.255
        gateway 10.0.0.XXX # your vm bridge
        # dns-* options are implemented by the resolvconf package, if installed
        dns-nameservers DNS-IP   </code></pre>




