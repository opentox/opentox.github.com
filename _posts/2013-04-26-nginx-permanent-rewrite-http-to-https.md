---
layout: post
title: "nginx permanent rewrite http to https"
description: ""
category: "Installation"
tags: ["Linux", "Nginx"]
---
{% include JB/setup %}

For permanent rewrite a http to a https adress in your nginx site-available config file use:

    server {
       listen         80;
       server_name    my.domain.com;
       rewrite        ^ https://$server_name$request_uri? permanent;
    }

    server {
       listen         443;
       server_name    my.domain.com;

       ssl            on;

       [....]
    } 
