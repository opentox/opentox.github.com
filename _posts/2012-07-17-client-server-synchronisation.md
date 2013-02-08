---
layout: post
title: "Client - server synchronisation"
description: ""
category: Usage
tags: []
---
{% include JB/setup %}
OpenTox objects and their representation at webservices live independent of each other. You can create and manipulate objects at the client side without affecting their representation at the webservice. On the other hand the server side representation might change while you are working locally on an object with the same address.

For synchronisation between client and server representations you can use the standard REST methods `get`, `post` (rarely useful), `put` and `delete`.

Examples:
---------

`get` To obtain a resource representation from the webservice

    feature = OpenTox::Feature.new my_uri, @@subjectid
    # feature is more or less empty, apart from some default entries
    feature.get # download metadata
    # work with the representation from the webservice
    puts feature.metadata.inspect
    ...

`put` Save a local object at the webservice

    feature = OpenTox::Feature.new nil, @@subjectid # create a new feature
    feature.title = "My test feature" # set the title
    feature[RDF.type] = RDF::OT.NominalFeature # add a feature type
    feature.put # save the feature at the webservice

`delete` Delete object at webservice

    feature = OpenTox::Feature.new my_uri, @@subjectid
    feature.delete
    feature.get # fails

Special cases:
--------------

*datasets*: Use `Dataset#get_metadata` to download only metadata. `Dataset#get` will retrieve all data entries.


