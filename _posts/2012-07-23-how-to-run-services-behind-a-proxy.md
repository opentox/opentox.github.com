---
layout: post
title: "How to run webservices behind a proxy"
description: ""
category: 
tags: [installation, coifiguration]
---
{% include JB/setup %}
Assigning a IP address to the full hostame in `etc/hosts` may cause uri problems when a proxy rewrites http to https. The solution is to remove all full hostname entries from `/etc/hosts` - after that all requests will have to pass the proxy.

