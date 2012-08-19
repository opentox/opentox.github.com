---
layout: post
title: "4store setup"
description: ""
category: setup
tags: [4store]
---
{% include JB/setup %}
It is important for large datasets/investigations to disable 4store soft limits - otherwise queries and downloads will be truncated. You can achieve that by setting

    [default]
      soft-limit = 0

to the 4store config file (`/etc/4store.conf` for global installations).
