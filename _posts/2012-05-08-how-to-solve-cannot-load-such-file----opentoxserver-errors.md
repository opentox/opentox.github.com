---
layout: post
title: "How to solve 'cannot load such file opentox/server' errors whens starting unicorn"
description: ""
category: Installation
tags: ["Debugging"]
---
{% include JB/setup %}

If you encounter such an error message during unicorn startup it is likely that one of the gems required by opentox-server does not load properly. 
Frequent causes are

- incorrect or missing configuration files (in ~/.opentox/config/service_name.rb)
- incompatible gems

Try to require opentox-server manually in irb. This should give you a more helpful error message. If a particular gem version does not work you can request a working version in opentox-server.gemspec.

