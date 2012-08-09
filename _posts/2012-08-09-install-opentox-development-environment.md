---
layout: post
title: "Install opentox development environment"
description: ""
category: General
tags: [deployment, installation, Ubuntu, Debian, Setup]
---
{% include JB/setup %}

HowTo install recent opentox services on Ubuntu or Debian

# Installation:

Installation (`development` at the point of writing this = 09/09/2012) is tested with Debian 6.0.5 and should work with recent Ubuntu versions. Installer is available at github. Please follow instruction:

    sudo apt-get install git-core 
    # please add you ssh keys at github. You may add your name/email adress to the local git config.
    cd ~
    git clone git@github.com:opentox/install.git
    cd install
    git checkout development
    # check config.sh
    ./install

This installation script will prepare your system for the opentox services, including installing necessary dependencies, opentox config, raptor, rasqal and 4store. 

# Install opentox services

    cd $OT_PREFIX # check path in config.sh 
    # Download opentox-client opentox-server opentox-test algorithm dataset feature and task
    for f in opentox-client opentox-server opentox-test algorithm dataset feature task; do git clone "git@github.com:opentox/$f.git"; cd $f; git checkout development; git checkout migration; cd ..; done 
    # install services with script (bundle)
    cd $OT_PREFIX/opentox-client/bin 
    ./opentox-client-install
    cd $OT_PREFIX/opentox-server/bin
    ./opentox-server-install
    for f in algorithm dataset feature task; do cd $f/bin; ./$f-install; cd ../..; done
    if ! cat $HOME/.opentox/config/default.rb | grep "compound">/dev/null 2>&1; then echo '$compound = { :uri => "http://webservices.in-silico.ch/compound" }' >> $HOME/.opentox/config/default.rb; fi    
    
# Add ot-tools to your .bashrc

    echo '. ~/install/ot-tools-user.sh' >> ~/.bashrc

# Run services

    otstart all 
