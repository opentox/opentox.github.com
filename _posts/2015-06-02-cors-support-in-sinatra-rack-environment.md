---
layout: post
title: "CORS support in Sinatra Rack environment"
description: "How to add CORS support to Sinatra Rack environment, Nginx or Apache2."
category: Installation
tags: [Setup, apache, Nginx, ruby]
---
{% include JB/setup %}
**This post explains how to enable CORS support to Sinatra Rack environment, Nginx or Apache2.**

**see also:** 

  * [http://en.wikipedia.org/wiki/Cross-origin_resource_sharing](http://en.wikipedia.org/wiki/Cross-origin_resource_sharing)
  * [http://enable-cors.org/server.html](http://enable-cors.org/server.html) 

CORS support for webservers 
---------------------------
to deliver static webpages with a webserver:

add headers in Apache2:

        Header set Access-Control-Allow-Origin "*"
        Header set Access-Control-Allow-Credentials true
        Header set Access-Control-Allow-Methods "GET, POST, DELETE, PUT, PATCH, OPTIONS"
        Header set Access-Control-Allow-Headers "Content-Type, api_key, Authorization"

add headers in nginx:

        add_header 'Access-Control-Allow-Methods' 'GET, POST, DELETE, PUT, PATCH, OPTIONS';
        add_header 'Access-Control-Allow-Credentials' 'true';
        add_header 'Access-Control-Allow-Origin' "*";
        add_header 'Access-Control-Allow-Headers' 'Origin, X-Requested-With, Content-Type, api_key, Authorization';

CORS support in Sinatra Rack environment
----------------------------------------

add to Gemfile:

```ruby
    gem 'rack-cors', :require => 'rack/cors'
```

in application code add header to routes:

```ruby
    class Application < Service
  
      use Rack::Cors do |config|
        config.allow do |allow|
           allow.origins '*'
           allow.resource '/file/list_all/', :headers => :any
           allow.resource '/file/at/*',
             :methods => [:get, :post, :put, :delete],
             :headers => :any,
             :max_age => 0
           allow.resource '/compound/*',
             :methods => [:get, :post],
             :headers => :any,
             :max_age => 0
        end
      end
```

**NOTE:** headers shouldn't be set both in webserver and rack. 
Otherwise you have doubled values and it didn't work for some applications:
e.g.: `Access-Control-Allow-Credentials: true, true` 

CORS support in OpenTox ruby applications 
-----------------------------------------
to have a configurable CORS support to opentox ruby webservices we can add a switch to the already existing configuration file `.opentox/config/default.rb` entry of a specific webservice. 
e.g.: in the definition of the compound webservice add `:cors => true` to enable CORS.

```ruby 
  $compound = { :uri => "https://myserver_name/compound", :cors => true }
```

and add modified application code to the opentox-server gem to deploy it to all webservices: 

```ruby
    class Application < Service

    # add CORS support for swagger
    if eval("$#{SERVICE}[:cors]") == true
      use Rack::Cors do |config|
        config.allow do |allow|
           allow.origins '*'
           allow.resource "/#{SERVICE}/*",
             :methods => [:get, :post, :put, :delete, :patch, :options],
             :headers => :any,
             :max_age => 0
        end
      end
    end
```
this works in Unicorn-Rack-Sinatra environment behind an Apache or Nginx proxy. Apache or Nginx do not add headers. 

