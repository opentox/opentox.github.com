---
layout: post
title: "error message segmentation faults"
description: "sometimes error messge appears about segmentation faults"
category: Client 
tags: [Debugging]
---
{% include JB/setup %}

===

Sometimes you get error messages like the following if you try to handle huge amount of rdf graphs.

  .rbenv/versions/1.9.3-p194/lib/ruby/gems/1.9.1/gems/addressable-2.2.8/lib/addressable/uri.rb:1319: [BUG] Segmentation fault
ruby 1.9.3p194 (2012-04-20 revision 35410) [x86_64-linux]<br>
  .rbenv/versions/1.9.3-p194/lib/ruby/gems/1.9.1/gems/rdf-0.3.5.2/lib/rdf/model/uri.rb:85:in initialize: method method_missing called on terminated object (0x000000188b67b8 flags=0x0 klass=0x0) (NotImplementedError)<br>
  .rbenv/versions/1.9.3-p194/lib/ruby/gems/1.9.1/gems/addressable-2.2.8/lib/addressable/uri.rb:101:in rescue in parse: Cant convert Array into String. (TypeError)<br>

Make sure you are using at least version 0.3.8 of rdf gem (`bundle install` again):

Gemfile.lock
---
    rdf (0.3.8)
      addressable (>= 2.2.6)
---
