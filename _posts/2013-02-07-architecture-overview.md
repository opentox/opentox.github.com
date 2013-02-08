---
layout: post
title: "Architecture overview"
description: "High level oeverview about the IST OpenTox architecture"
category: Development
tags: [Architecture]
---
{% include JB/setup %}

The IST OpenTox architecture consists of 3 main layers. [OpenTox](http://opentox.org) compliant **[webservices](https://en.wikipedia.org/wiki/Web_service)** are the core layer, that provide the functionality for OpenTox [classes](http://opentox.org/dev/apis). **Clients** (e.g. libraries for various programming languages and command line/graphical user interfaces) interact with webservices and provide interfaces for end users. Webservices may depend on **backends** for special purposes (e.g. databases, A+A servers, number crunching). Clients should **never** interact with backends directly.

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

  **IST**: compound, feature, dataset, investigation, algorithm, model, task, a+a    
  **ALU**: validation    
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

