---
layout: post
title: "CoreOS Installation on a Hetzner Server"
description: ""
category: 
tags: []
---
{% include JB/setup %}

- log into the rescue system

    ssh root@{sever-ip}

- run `installimage`

  - choose "Other" -> "CoreOS-XXX"
  - keep the default install.conf (you may have to quit with ESC 0, if F10 does not work)
  - confirm deletion of partitions

* reboot into CoreOS

    reboot

- log into CoreOS (using the same password as for the rescue system)

    ssh root@{sever-ip}

- set the hostname (if you have omitted it in install.conf)

    hostname {hostname}

- change root password

    passwd

- create a password for the `core` user

    passwd core

- login as core user from another console to make sure everything works as expected (and to avoid lockouts)

- disable root login

    - edit /etc/ssh/sshd_config 
    - set PermitRootLogin no
    - restart sshd

        sudo systemctl daemon-reload

- exit root

    exit


