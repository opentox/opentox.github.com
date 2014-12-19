---
layout: post
title: "configure public folder for sinatra application"
description: ""
category: 
tags: ["sinatra", "webservice"]
---
{% include JB/setup %}

Point in your sinata application.rb to public folder if running parallel services by one server (apache, nginx).

E.G. nginx (available sites):

    server{
      ....
      ....
      ....
      
      location/SERVICE1 {
        
        proxy ...
        ....
      }
      
      location/SERVICE2 {
      
        proxy ...
        ....
      }


application.rb

    require ...
    
    set :public_folder, 'SERVICE1/public'
    

This is necessary to proper serve files from your applications public folder like images.
