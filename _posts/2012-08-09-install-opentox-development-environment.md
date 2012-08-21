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

Installation (`development` at the point of writing this = 20/08/2012) is tested with Debian 6.0.5 and should work with recent Ubuntu versions. Installer is available at [github](https://github.com/opentox/install).

    # Check if "sudo" is available (e.g. sudo ls)
    # Install git
    sudo apt-get install git 
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

    . $HOME/.opentox/config/install/config.sh
    . $OT_PREFIX/install/utils.sh
    # Download opentox-client opentox-server 
    for f in opentox-client opentox-server; do 
      git clone "git@github.com:opentox/$f.git" $OT_PREFIX/$f
      cd $OT_PREFIX/$f
      git checkout development >/dev/null 2>&1
      git checkout migration >/dev/null 2>&1
    done 
    # Install services with script (bundle)
    cd $OT_PREFIX/opentox-client/bin 
    ./opentox-client-install 
    # This script might fail after installing ruby, please start script again. 
    # Will be fixed soon.
    cd $OT_PREFIX/opentox-server/bin
    ./opentox-server-install
    # Download and install algorithm compound dataset feature task opentox-test 
    for f in algorithm compound dataset feature task opentox-test; do
      git clone "git@github.com:opentox/$f.git" $OT_PREFIX/$f
      cd $OT_PREFIX/$f
      git checkout development >/dev/null 2>&1
      if [ -f $OT_PREFIX/$f/bin/$f-install ] then
        cd $OT_PREFIX/$f/bin
        ./$f-install    
      fi
    done

At this point `opentox` web services have been downloaded, installed and configured.   
    
# Activate and use ot-tools if you are using bash

NOTE: ot-tools do require the use of bash shell.

Add ot-tools to your .bashrc:

    echo '. $HOME/.opentox/ot-tools.sh' >> ~/.bashrc
    # source .bashrc or restart shell

All ot-tools (`otconfig`, `otstart`, `otcheck`, `otreload`, `otkill`) are available in your shell. General usage: 

    [otstart|otcheck|otreload|otkill] [all|algorithm|compound|dataset|feature|
    model|task|validation|4store]

`otconfig` is the only command not taking an argument, it sets the environment variables for all services. 

Start all services and their 4store backbones with:

    otstart all 

Kill all services and their 4store backbones with:
    
    otkill all

It is possible to manage single services and its 4store backbone(e.g. `otstart dataset` to run dataset web service). 

# Access web services

The default port setting for the web services and their 4store backends,  (see config files ~/.opentox/config/\*.rb):

<table border="1">
<tr>
<th>Service</th>
<th>URI</th>
</tr>
<tr>
<td>Algorithm<br>Algorithm 4store backend</td>
<td>localhost:8081/algorithm<br>localhost:9081</td>
</tr>
<tr>
<td>Compound<br>Compound 4store backend</td>
<td>localhost:8082/compound<br>localhost:9082</td>
</tr>
<tr>
<td>Dataset<br>Dataset 4store backend</td>
<td>localhost:8083/dataset<br>localhost:9083</td>
</tr>
<tr>
<td>Feature<br>Feature 4store backend</td>
<td>localhost:8084/feature<br>localhost:9084</td>
</tr>
<tr>
<td>Model<br>Model 4store backend</td>
<td>localhost:8085/model<br>localhost:9085</td>
</tr>
<tr>
<td>Task<br>Task 4store backend</td>
<td>localhost:8086/task<br>localhost:9086</td>
</tr>
<tr>
<td>Validation<br>Validation 4store backend</td>
<td>localhost:8087/validation<br>localhost:9087</td>
</tr>
</table> 

NOTE: Only installed service uris are set in the config files. Model and validation service are not available yet. 

# Testing services

    cd $OT_PREFIX/opentox-test
    otconfig
    ruby opentox-client.rb

The tests are running for some time and will finish with a short report:
    
    Finished tests in 49.466044s, 0.8086 tests/s, 3.8410 assertions/s.
    40 tests, 190 assertions, 0 failures, 1 errors, 0 skips

To test one specific service, run its script (ruby [service_name].rb) located in $OT_PREFIX/opentox-test/test.

