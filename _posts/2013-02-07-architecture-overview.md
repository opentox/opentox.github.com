---
layout: post
title: "Architecture overview"
description: "High level oeverview about the IST OpenTox architecture"
category: Development
tags: [Architecture]
---
{% include JB/setup %}

The IST OpenTox architecture consists of 3 main layers. [OpenTox](http://opentox.org) compliant **[webservices](https://en.wikipedia.org/wiki/Web_service)** are the core layer, that provide the functionality for OpenTox [classes](http://opentox.org/dev/apis). **Clients** (e.g. libraries for various programming languages and command line/graphical user interfaces) interact with webservices and provide interfaces for end users. Webservices may depend on **backends** for special purposes (e.g. databases, A+A servers, number crunching). Clients should **never** interact directly with backends.

OpenTox services can run on separate servers (*cloud configuration*) or on a single machine (*standalone configuration*) even without internet connection.

Clients:
--------

- use the OpenTox/ToxBank REST [API](http://opentox.org/dev/apis) to communicate with webservices 

### Examples:   

  **GUIs**: [ToxCreate](http://toxcreate.org), [ToxPredict](http://toxpredict.org), [lazar](http://lazar.in-silico.ch), toxbank protocol and investigation GUIs, [Bioclipse](http://www.bioclipse.net/), ...    
  **CLIs and scripts**: validation scripts for algorithm model development, irb using [opentox-client](http://github.com/opentox/opentox-client), [curl](http://curl.haxx.se/), [opentox-test](http://github.com/opentox/opentox-test), ...    
  **Libraries**: e.g. the [opentox-client](http://github.com/opentox/opentox-client) Ruby library

Webservices:
------------

- use the OpenTox/ToxBank REST [API](http://opentox.org/dev/apis) to communicate with other webservices 
- use application specific protocols to communicate with backends (implementation specific and optional)

### Examples:    

  **IST**: [compound](http://github.com/opentox/compound), [feature](http://github.com/opentox/feature), [dataset](http://github.com/opentox/dataset), [algorithm](http://github.com/opentox/algorithm), [model](http://github.com/opentox/model), [validation](http://github.com/opentox/validation), [investigation](http://github.com/toxbank/toxbank-investigation), [task](http://github.com/opentox/task)) 
  **IDEA**: dataset (AMBIT), algorithm, protocol    
  **TUM**: algorithm, model    
  **NTUA**: algorithm, model    

Backends: 
---------

- optimized for particular purposes (data storage and retrieval, number crunching, ...)
- never addressed directly from clients or 3rd party webservices
- one backend may be used for multiple services

### Examples:    

  **IST**: {Redis}, [4store](http://www.4store.org/), \[Rserve\], [OpenSSO](https://en.wikipedia.org/wiki/OpenSSO)    
  **External**: [CACTVS](http://cactus.nci.nih.gov/), [PubChem](https://pubchem.ncbi.nlm.nih.gov/)    
    {} IST services that may be removed in the future    
    \[\] IST services that may be added in the future    

Implementation
--------------

IST OpenTox services and applications are implemented in predominately [Ruby](http://ruby-lang.org) and distributed as [RubyGems](https://rubygems.org/). The [opentox-client](http://github.com/opentox/opentox-client) library is a wrapper for the OpenTox [API](http://opentox.org/dev/apis) which is used in client applications and webservices. The [opentox-server](http://github.com/opentox/opentox-server) library depends on [opentox-client](http://github.com/opentox/opentox-client) and provides a basic web server infrastructure for web applications and webservices based on the [Sinatra](http://www.sinatrarb.com/) framework. It implements common functionality like

- content negotiation
- authentication and authorisation
- configuration handling
- basic CRUD operations

OpenTox webservices (e.g. [compound](http://github.com/opentox/compound), [feature](http://github.com/opentox/feature), [dataset](http://github.com/opentox/dataset), [algorithm](http://github.com/opentox/algorithm), [model](http://github.com/opentox/model), [validation](http://github.com/opentox/validation), [investigation](http://github.com/toxbank/toxbank-investigation), [task](http://github.com/opentox/task)) extend [opentox-server](http://github.com/opentox/opentox-server). 

Tests
-----

OpenTox tests are maintained in a separate package called [opentox-test](http://github.com/opentox/opentox-test) which includes

- client tests
- webservice tests
- integration tests
- cloud configuration tests 
- standalone configuration tests 

The complete test suite in [opentox-test](http://github.com/opentox/opentox-test) is executed once a day for the master and development branch of all webservices.

Configuration
-------------

URIs and credentials for webservices and backends are configured in ~/.opentox/config/:

- default.rb: default configuration for [opentox-server](http://github.com/opentox/opentox-server)
- {service_name}.rb: overwrite/extends default.rb 
- test.rb: test configuration

Configuration details can be found [here](/tags.html#Configuration-ref).
