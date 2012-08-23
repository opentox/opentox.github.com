---
layout: post
title: "Install production webservice gems"
description: ""
category: general
tags: [installation, gem]
---
{% include JB/setup %}
The installation of opentox-webservices is straightforward with the `gem` command, e.g.

    gem install opentox-dataset 
    gem install toxbank-investigation

Please do not forget to configure the webservice in `~/.opentox/config/<webservice>.rb`.
In some cases (eg. compound and algorithm) large external libraries have to be compiled - please be patient.
