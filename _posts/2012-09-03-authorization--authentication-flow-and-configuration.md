---
layout: post
title: "Authorization &#38; Authentication Flow and Configuration"
description: "Authorization & Authentication flow for webservices using the opentox-ruby gem"
category: server
tags: [Setup, webservices]
---
{% include JB/setup %}




A&A Flow opentox-ruby gem
=========

Graphical Flow of Authorization & Authentication for webservices using the opentox-ruby gem. Webservice configuration is located in **/home/USERNAME/.opentox/config/ENVIRONMENT.yaml**  
 
![Authorization Authentication](/images/aa_flow.gif)


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

   