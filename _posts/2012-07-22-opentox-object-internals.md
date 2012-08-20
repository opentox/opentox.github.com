---
layout: post
title: "OpenTox object internals"
description: ""
category: client
tags: [development]
---
{% include JB/setup %}
Default OpenTox objects are defined in `opentox-client/lib/opentox.rb`. They contain metadata as `RDF::Graph` in the `@rdf` variable.

Accessing metadata
==================

The `OpenTox#metadata` method returns object metadata as a hash with predicates as keys and objects as values. The  `[]` shortcut gives direct access to metadata values (`object[RDF::DC.title]` returns e.g. the objects title). An array is returned if multiple entries are available for the predicate (`feature[RDF.type]` may return e.g. `[RDF::OT.Feature, RDF::OT.StringFeature, RDF::OT.NominalFeature]`).

Shortcut methods exist for frequently used predicates, you may use e.g. `object.title` instead of `object[RDF::DC.title]`.

Setting metadata
================

There are two methods to assign metadata: `object[predicate] = value` assigns new value(s) to a predicate (replacing existing values) while `object.append subject, value` appends metadata to existing entries. In both cases `value` can be either a single entry or an array of values. You can also use convenience shortcuts like `object.title = new_title`.

[Client - server synchronisation](/opentox-client/2012/07/17/client-server-synchronisation)
===========================================================================================

