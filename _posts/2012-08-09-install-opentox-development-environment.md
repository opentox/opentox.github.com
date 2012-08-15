---
layout: post
title: "Install opentox development environment"
description: ""
category: General
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
    if ! cat $HOME/.opentox/config/default.rb | grep "compound">/dev/null 2>&1; then i
      echo '$compound = { :uri => "http://webservices.in-silico.ch/compound" }' >> $HOME/.opentox/config/default.rb
    fi 

Now, `opentox` web services are downloaded, installed and configured.   
    
# Activate and use ot-tools

Add ot-tools to your .bashrc:

    echo '. ~/.opentox/ot-tools.sh' >> ~/.bashrc
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

The default port setting (see default config file ~/.opentox/config/default.rb):

    Algorithm: localhost:8081/algorithm
    Compound: localhost:8082/compound
    Dataset: localhost:8083/dataset
    Feature: localhost:8084/feature
    Model: localhost:8085/model
    Task: localhost:8086/task
    Validation: localhost:8087/validation
    4store: localhost:8088/

NOTE: Only installed service uris are set in the config files. Model and validation service are not available yet and an external compound services is used. 

# Testing services

    cp $HOME/.opentox/config/default.rb $HOME/.opentox/config/test.rb  # This is a workaround, will be fixed soon.
    cd $OT_PREFIX/opentox-test
    otconfig
    ruby opentox-client.rb

The tests are running for some time and will finish with a short report:
    
    Finished tests in 50.728600s, 0.7885 tests/s, 3.1935 assertions/s.
    40 tests, 162 assertions, 0 failures, 0 errors, 0 skips

To test one specific service run its script (ruby [service_name].rb) located in $OT_PREFIX/opentox-test/test.

