---
layout: post
title: "Authorization & Authentication Configuration (opentox (server/client) >= 3.0.0)"
description: "Authorization & Authentication Configuration for opentox-client opentox-server >= Version 3.0.0"
category: Installation
tags: [Configuration, Setup, Webservices]
---
{% include JB/setup %}

This page describes the newer Authorization & Authentication configuration for opentox webservices. Configuration has changed from a single YAML file to SERVICENAME.rb files for each service. Opentox webservices (algorithm, compound, dataset, feature, model, task, validation) do load `/home/USERNAME/.opentox/config/default.rb` on startup for default or global settings. After **default.rb** each service loads its own configuration file `/home/USERNAME/.opentox/config/SERVICENAME.rb`. This may also overwrite settings from **default.rb**.         

A&A How it works
----
[Description](/Installation/2012/09/03/authorization--authentication-flow-and-configuration/) see paragraphs **A&A How it works** and **A&A Flow opentox-ruby gem** for Versions below 3.0.0 .


Configuration-file example
----
Example A&A configuration for the dataset service. Configuration-file is **/home/USERNAME/.opentox/config/dataset.rb**  

    $dataset = { :uri => "https://myserver.org/dataset" }
    $aa = {
      :uri => 'https://opensso.in-silico.ch',
      :free_request => [:HEAD],
      :authenticate_request => [],
      :authorize_request => [:GET, :POST, :DELETE, :PUT],
      :authorize_exceptions => { [:GET,:POST] => [$dataset[:uri], "#{$dataset[:uri]}/test/task_error", "#{$dataset[:uri]}/test/error_in_task"] }
    }

Configuration options
----
* `$dataset = { :uri => "https://myserver.org/dataset" }` sets the URI to the dataset service itself.
* `$aa = { :uri => nil }` turns A&A off.
* `:uri => 'https://opensso.in-silico.ch'` set the OpenSSO server URI.
* `:free_request => [:HEAD]` define all free request methods. Set HEAD method as a free request method for internal control calls.
* `:authenticate_request => []` request methods that needs only Authentification. Request must have a valid subjectid, but no policy.
* `:authorize_request => [:GET, :POST, :DELETE, :PUT]` request methods with authorization controll
* `:authorize_exceptions => { [:GET,:POST] => [$dataset[:uri], "#{$dataset[:uri]}/test/task_error", "#{$dataset[:uri]}/test/error_in_task"] }` authorization exceptions defined as a Hash of request-methods-array keys mapped to URI-array values. In this case you are allowed to GET and POST to the base service URI to get a list of datasets or to POST a new dataset.
  `"#{$dataset[:uri]}/test/error_in_task"` defines a URI used in a test call.


Configuration-file example for Validation service
----
Example A&A configuration for the validation service. Configuration-file is **/home/USERNAME/.opentox/config/validation.rb**

Mind the `/#{Regexp.quote($validation[:uri])}\/[a-z,A-Z,\/,_\-]*$/`  entry in authorize exceptions to let authenticated users the possibility to create crossvalidations, reports and other objects under `/validation/` directory.

    $validation = { :uri => "https://myserver.org/validation" }
    $aa = {
      :uri => 'https://opensso.in-silico.ch',
      :free_request => [:HEAD],
      :authenticate_request => [],
      :authorize_request => [:GET, :POST, :DELETE, :PUT],
      :authorize_exceptions => {[:GET,:POST] => [$validation[:uri], /#{Regexp.quote($validation[:uri])}\/[a-z,A-Z,\/,_\-]*$/, $task[:uri]]}
    }
