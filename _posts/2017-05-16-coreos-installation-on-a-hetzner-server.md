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
  - keep the default install.conf (you may have to quit with ESC 0, if F10 does not work)
  - confirm deletion of partitions

* reboot into CoreOS

    `reboot`

- log into CoreOS (using the same password as for the rescue system)

    `ssh root@{sever-ip}`

CoreOS overwrites user data in /etc during booting (e.g. after a automatic system update). In order to make persistent changes, we have to edit
`/var/lib/coreos-install/user_data`:

    - set the hostname
    - add your public SSH key for the core user
    - disable sftp `#Subsystem sftp internal-sftp`
    - disable root login `PermitRootLogin no`
    - disable password authentication `PasswordAuthentication no`

The docker systemd service is not enabled by default, but we need it to restart docker services after a reboot:

    `systemctl enable docker.service`

Reboot to test changes:

    `reboot`

Make sure you can login as core user:

    `ssh core@{sever-ip}`

