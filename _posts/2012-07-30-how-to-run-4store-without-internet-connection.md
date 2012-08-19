---
layout: post
title: "How to run 4store without internet connection"
description: ""
category: setup
tags: [4store, local installation]
---
{% include JB/setup %}

4store needs a running avahi-daemeon. It cannot connect to a backend without an IP address. If you intend to run 4store on a computer without internet connection you can use `avahi-autoipd`, starting it eg. with `sudo avahi-autoipd eth0 -D`.
