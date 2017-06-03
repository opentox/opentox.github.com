---
layout: post
title: "CoreOS Installation on a Hetzner Server"
description: ""
category: 
tags: []
---
{% include JB/setup %}

- log into the rescue system

    `ssh root@{sever-ip}`

- run `installimage`

  - choose "Other" -> "CoreOS-XXX"
  - set the hostname
  - keep the remaining defaults in install.conf (you may have to quit with ESC 0, if F10 does not work)
  - confirm deletion of partitions

Beware: Contrary to the output info `installimage` does not create a software raid. CoreOS is installed on /dev/sda, /dev/sdb remains unpartitioned.

* adjust CoreOS configuration

  - mount ROOT partition

    `mount /dev/sda9 /mnt`

  - add keys to `/mnt/home/core/.ssh/authorized_keys`
  * disable root and password logins

    ```
    echo "PermitRootLogin no" > /mnt/etc/ssh/sshd_config
    echo "PasswordAuthentication no" >> /mnt/etc/ssh/sshd_config
    ```

  - remove user and sshd entries from coreos-install (to make above changes permanent between reboots)

    ```
    cp /mnt/var/lib/coreos-install/user_data /mnt/var/lib/coreos-install/user_data~
    sed -i '/users:/,$d' /mnt/var/lib/coreos-install/user_data
    ```

* reboot into CoreOS

    `reboot`

- log into CoreOS

    `ssh core@{sever-ip}`

The docker systemd service is not enabled by default, but we need it to restart docker services after a reboot:

    `sudo systemctl start docker.service`
    `sudo systemctl enable docker.service`


