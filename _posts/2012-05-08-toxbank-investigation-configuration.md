---
layout: post
title: "OpenTox/ToxBank Webservices Configuration"
description: "Example Configuration for OpenTox/ToxBank webservices"
category: opentox-server
tags: [Setup]
---
{% include JB/setup %}

# Example Configuration for OpenTox/ToxBank webservices
Configuration file is usually at **/home/toxbank/.opentox/config/webservicename.rb**

    $task = {
      :uri => 'http://webservices.myserver.org/task'
    }
    $four_store = {
      :uri => 'https://my4storeserver.org',
      :user => 'myuser',
      :password => 'mysecretpassword',
    }
    $aa = {
      :uri => 'https://opensso.in-silico.ch',
      :free_request => [:HEAD],
      :authenticate_request => [],
      :authorize_request => [:GET, :POST, :DELETE, :PUT],
      :authorize_exceptions => {[:GET, :POST, :HEAD] => ['https://my-toxbank-investigation.org', $task[:uri]]}
    }


