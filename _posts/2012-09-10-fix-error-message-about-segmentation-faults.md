---
layout: post
title: "fix error/bug message about segmentation faults"
description: "possible fix for error/bug message about segmentation faults"
category: general 
tags: [debugging, curl]
---
{% include JB/setup %}

===
If you get error messages like:

  .rbenv/versions/1.9.3-p194/lib/ruby/gems/1.9.1/gems/addressable-2.2.8/lib/addressable/uri.rb:1319: [BUG] Segmentation fault
ruby 1.9.3p194 (2012-04-20 revision 35410) [x86_64-linux]<br>
  .rbenv/versions/1.9.3-p194/lib/ruby/gems/1.9.1/gems/rdf-0.3.5.2/lib/rdf/model/uri.rb:85:in initialize: method method_missing called on terminated object (0x000000188b67b8 flags=0x0 klass=0x0) (NotImplementedError) 

It looks like the error that libcurl version 7.21 have so i decided first to compile and install version 7.26 to fix this bug, without success.<br> 
In fact that e.g.installing libcurl3-dev or other maybe missing curl libs with aptitude alert that libcurl4-gnutls-dev and libraptor1-dev would be removed i decided to simply uninstall curl and reinstall it again, with success !

In my case i executed a ruby script within a backtick curl command, that pulls huge amount of rdf graphs into an array.

It seems like sth. to do with the install order of curl after some rubygems ? Maybe it is possible to configure the path to curl while installing the gems through Bundler ? Installed curl version  is now 7.21.0-2.1+squeeze2 again.
