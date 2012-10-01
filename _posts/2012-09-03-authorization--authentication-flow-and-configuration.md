---
layout: post
title: "Authorization &#38; Authentication Flow and Configuration"
description: "Authorization & Authentication flow for webservices using the opentox-ruby gem"
category: server
tags: [Setup, webservices]
---
{% include JB/setup %}


A&A How it works
=========
A&A is located in a before filter method of the ruby framework sinatra, which means every request (REST) to the sinatra application is controlled by the code inside this filter befor anything else happens.

If A&A is activated: 

1.  every request is disallowed.
2.  you can define exeptions in the configuration file => __CONFIGURATION__
  *  authentification exeptions
  *  authorization exeptions
3.   you can allow one or more REST request (GET, POST, PUT, DELETE) to an URI controlled by the openSSO server, defined in policies. => __POLICIES__


A&A Flow opentox-ruby gem
=========

Graphical Flow of Authorization & Authentication for webservices using the opentox-ruby gem. Webservice configuration is located in **/home/USERNAME/.opentox/config/ENVIRONMENT.yaml**  
 
![Authorization Authentication](/images/aa_flow.gif)


Configuration options
=========
inside the authorization block (see also configuration example)
* `server`- openSSO server URI. If this line is set to `:server: ` A&A is disabled.
* `free_request` - requests without any Authorization and Authentication **GET, POST, PUT, DELETE** (maybe also HEAD in newer Versions)
* `authenticate_request` - requests with Authentication but without Authorization **GET, POST, PUT, DELETE**
* `authorize_request` - requests controlled by  Authentication and Authorization **GET, POST, PUT, DELETE**
* `free_uris` - YAML hash of requests and URIs not controlled by A&A 
* `authorize_exceptions` -  YAML hash of requests and URIs only authenticated, without authorization (user has to be logged in)


<br />
String or regex URIs
=========
URIs in `free_uris` and `authorize_exceptions` are possible as string or regex
e.G.: 

      :free_uris:
        ? - :GET
        : - !ruby/regexp /http:\/\/SERVER\/algorithm/
          - "http://SERVER/model"

defines free access to GET request for "http://SEVER/model" exact URI, not to "http://SERVER/model/ID".
the regex `!ruby/regexp /http:\/\/SERVER\/algorithm/` gives free GET request to "http://SERVER/agorithm/" and all addresses below like "http://SERVER/algorithm/bbrc/".

Configuration-file example
=========

    # OpenSSO Authorization
    # set ":server: " to disable A&A
    :authorization:
      :server: https://opensso.in-silico.ch
      :free_request: #request-method not controlled by A&A
        - ""
      :authenticate_request: #only for authenticated user
        - ""
      :authorize_request:  #only for authenticated and authorized user
        - "GET"
        - "POST"
        - "HEAD"
        - "DELETE"
        - "PUT"
    # Exceptions:
      :free_uris: #request-method for uri not controlled by A&A
        ? - :GET
        : - !ruby/regexp /http:\/\/SERVER\/algorithm/
          - "http://SERVER/model"
          - "http://SERVER/dataset"
          - "http://SERVER/validation"
          - "http://SERVER/toxcreate"
          - !ruby/regexp /http:\/\/SERVER\/validation\/resources/
          - !ruby/regexp /http:\/\/SERVER\/validation\/[a-z,A-Z,\/,_\-]*$/
        ? - :GET
          - :POST
          - :HEAD
        : - !ruby/regexp /http:\/\/SERVER\/toxcreate\/[a-z,A-Z,\/,_\-]*$/
          - !ruby/regexp /http:\/\/SERVER\/task/
          - !ruby/regexp /http:\/\/SERVER\/compound/
          - !ruby/regexp /sign_(in|out)$/
        ? - :PUT
        : - !ruby/regexp /http:\/\/SERVER\/task/
        ? - :DELETE
        : - "http://SERVER/task/cleanup"
    
      :authorize_exceptions: #request-method for uri only authenticated, no authorization
        ? - :POST
        : - !ruby/regexp /http:\/\/SERVER\/algorithm/
          - "http://SERVER/dataset"
          - "http://SERVER/model"
          - "http://SERVER/validation"
          - !ruby/regexp /http\:\/\/SERVER\/validation\/[a-z,A-Z,\/,_\-]*$/

   