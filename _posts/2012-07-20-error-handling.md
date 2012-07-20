---
layout: post
title: "Error handling"
description: ""
category: general
tags: [development]
---
{% include JB/setup %}
For developers only.

Rule 1
======

*Never* use `raise` to raise errors. `opentox-client/lob/error.rb` has predefined errors like `bad_request_error message, uri` that create OpenTox compatible error reports.

Rule 2
======

Avoid catching errors to eagerly. This may lead to obfuscated error messages that are hard to debug. The most obvious place where it makes sense to raise errors early is immediately before long computations (i.e. before spawning a `OpenTox::Task`. Here it makes sense to check for obvious mistakes in the input parameters and return a `bad_request_error` immediately to the client.

Disclaimer
==========

I am not 100% sure, if the whole error handling system is already properly implemented. Please contact helma _at_ in-silico.ch if you have any problems - ideally with a test that reproduces the problem.
