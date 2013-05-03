---
layout: post
title: "Example Webservice Configuration File"
description: ""
category: Installation
tags: [Configuration, Setup, Webservices]
---
{% include JB/setup %}

Example content of a webservice configuration file. The webservices config file (ruby .rb file) is located at /$HOME/.opentox/config/WEBSERVICE_NAME.rb 

    $task = {
      :uri => 'https://services.in-silico.ch/task'
    }
    $four_store = {
      :uri => 'https://4STORESERVER/',
      :user => '4STOREUSER',
      :password => '4STOREPASSWORD',
    }
    $aa = {
      :uri => 'https://opensso.in-silico.ch',
      :free_request => [:HEAD],
      :authenticate_request => [],
      :authorize_request => [:GET, :POST, :DELETE, :PUT],
      :authorize_exceptions => {[:GET, :POST, :HEAD] => ['https://SERVER/WEBSERVICE', $task[:uri]]}
    }
    $WEBSERVICE_NAME = {
      :uri => "https://SERVER/WEBSERVICE"
    }


<br />
## Example Details

* __$task__ Hash: defines the URI of the task service.
* __$four_store__ Hash: with values for URI, username and password to a 4store. 
* __$aa__ Hash: configuration for Authorization and Authentication server.

  * uri: of the opensso server.
    set __:uri => nil__ to disable A&A 
  * free_request: requests without A&A control
  * authenticate_request: requests with authentication only
  * authorize_request: requests with authentication and authorization
  * authorize_exceptions: URIs with authorization exceptions
    
* __$WEBSERVICE_NAME__ Hash: standard configuration of a the webservice. URI and anything else what is needed.


see also [webservice configuration example](/Installation/2013/05/03/authorization--authentication-configuration-opentox-serverclient--300/) for opentox-(client/server) version >= 3.0.0.  
