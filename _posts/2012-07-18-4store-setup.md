---
layout: post
title: "4store setup"
description: ""
category: general
tags: [4store]
---
{% include JB/setup %}
It is important for large datasets/investigations to disable 4store soft limits - otherwise queries and downloads will be truncated. You can achieve that by setting

    [default]
      soft-limit = 0

to /etc/4store.conf.
