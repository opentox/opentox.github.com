---
layout: post
title: "Error handling"
description: ""
category: Development
tags: [Error handling]
---
{% include JB/setup %}
For developers only.

Rule 1
======

*Never* use `raise` to raise errors. `opentox-client/lob/error.rb` provides predefined opentox error methodss like `bad_request_error message, uri` that create OpenTox compatible errors with meaningful reports, that are propagated through REST calls and Tasks. Errors are derived from `RestClient::STATUSES` (http://rdoc.info/github/archiloque/rest-client/RestClient), by converting them to underscores and appending `_error`. `RestClientWrapper.known_errors` lists all handled error methods.

Rule 2
======

Avoid catching errors to eagerly. This may lead to obfuscated error messages that are hard to debug. The most obvious place where it makes sense to raise errors early is immediately before long computations (i.e. before spawning a `OpenTox::Task`. Here it makes sense to check for obvious mistakes in the input parameters and return a `bad_request_error` immediately to the client.

