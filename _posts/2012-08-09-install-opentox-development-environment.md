---
layout: post
title: "Install opentox development environment"
description: ""
category: development
tags: [deployment, installation, Ubuntu, Debian, Setup]
---
{% include JB/setup %}

How To install recent opentox services on Ubuntu or Debian

# Installation:

Installation (`development` at the point of writing this = 15/08/2012) is tested with Debian 6.0.5 and should work with recent Ubuntu versions. Installer is available at [github](https://github.com/opentox/install). Please follow instruction (an internet connection is required):

    # Check if "sudo" is available (e.g. sudo ls)
    # Install git
    sudo apt-get install git-core 
    # Please add your ssh key at github via https://github.com/. 
    # You may add your name/email address to the local git config.
    # Download the installer from github. 
    git clone git@github.com:opentox/install.git $HOME/install
    cd $HOME/install
    git checkout development
    # First check config.sh
    ./install

This installation script will prepare your system for the use of `opentox` web services, including installing necessary dependencies, `opentox` config, `raptor`, `rasqal` and `4store`. 

# Install opentox services

    # Check OT_PREFIX in $HOME/install/config.sh 
    OT_PREFIX="path/to/directory"
    # Download opentox-client opentox-server opentox-test algorithm dataset feature and task
    for f in opentox-client opentox-server opentox-test algorithm dataset feature task; do 
      git clone "git@github.com:opentox/$f.git" $OT_PREFIX/$f
      cd $OT_PREFIX/$f
      git checkout development
      git checkout migration
    done 
    # Install services with script (bundle)
    cd $OT_PREFIX/opentox-client/bin 
    ./opentox-client-install 
    # This script might fail after installing ruby, please start script again. Will be fixed soon.
    cd $OT_PREFIX/opentox-server/bin
    ./opentox-server-install
    cd $OT_PREFIX
    for f in algorithm dataset feature task opentox-test; do
      cd $OT_PREFIX/$f/bin
      ./$f-install
    done

Now, `opentox` web services have been downloaded, installed and configured.   
    
# Activate and use ot-tools

Add ot-tools to your .bashrc:

    echo '. $HOME/.opentox/ot-tools.sh' >> ~/.bashrc
    # source .bashrc or restart shell

All ot-tools (`otconfig`, `otstart`, `otcheck`, `otreload`, `otkill`) are available in your shell. General usage: 

    [otstart|otcheck|otreload|otkill] [all|algorithm|compound|dataset|feature|model|task|validation|4store]

Only `otconfig` does not need an argument, it sets the environment variables for all services. 

Start all services with:

    otstart all 

Kill all services with:
    
    otkill all

It is possible to manage single services (e.g. `otstart dataset` to run dataset web service). 

# Access web services

The default port setting for the web services and their 4store backends,  (see config files ~/.opentox/config/\*.rb):

    Algorithm: localhost:8081/algorithm
    Algorithm 4store backend: localhost:9081
    Compound: localhost:8082/compound
    Compound 4store backend: localhost:9082
    Dataset: localhost:8083/dataset
    Dataset 4store backend: localhost:9083
    Feature: localhost:8084/feature
    Feature 4store backend: localhost:9084
    Model: localhost:8085/model
    Model 4store backend: localhost:9085
    Task: localhost:8086/task
    Task 4store backend: localhost:9086
    Validation: localhost:8087/validation
    Validation 4store backend: localhost:9087

    4store: localhost:8088/

NOTE: Only installed service uris are set in the config files. Model and validation service are not available yet and an external compound services is used. 

# Testing services

    cd $OT_PREFIX/opentox-test
    otconfig
    ruby opentox-client.rb

The tests are running for some time and will finish with a short report:
    
    Finished tests in 49.466044s, 0.8086 tests/s, 3.8410 assertions/s.
    40 tests, 190 assertions, 0 failures, 1 errors, 0 skips

To test one specific service run its script (ruby [service_name].rb) located in $OT_PREFIX/opentox-test/test.

