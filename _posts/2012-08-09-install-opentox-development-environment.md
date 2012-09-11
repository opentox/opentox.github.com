---
layout: post
title: "Install opentox development environment"
description: ""
category: setup
tags: [deployment, installation, Ubuntu, Debian, Setup]
---
{% include JB/setup %}

Architecture overview and how to install recent opentox services on Ubuntu or Debian.

# Philosophy

The [opentox installer](https://github.com/opentox/install/tree/development) provides a [POSIX](http://en.wikipedia.org/wiki/POSIX) compatible way to prepare Debian-based systems and install ISTs OpenTox compatible web-services and library code. Please report bugs [here](http://github.com/opentox/install).

Apart from some basic Debian packages, no action requires root access. Everything is installed in the user's home directory. Inside it, the installer prepares the base directory (referred to as `OT_PREFIX`) for OpenTox REST services and provides libraries for the installation and shell integration of services. 

Here are some more goals we had in mind when writing the installer:

- Safe (existence of all binaries will be checked before running, apart from GNU Core Utils)
- Idempotent (multiple execution incurs no changes to the system)
- Atomic (return value of non-elementary actions asserted to be TRUE)
- Encapsulated (everything installed in OT_PREFIX)
- Logged (all non-elementary actions are logged)

Conceptual approach:

1. Configure the installer in config.sh, then run `install`. 
2. Configure your system by adding a line to the startup file of your favorite shell (e.g. BASH with the file `~/.bashrc`) to read in `~/.opentox/opentox-ui.sh` (e.g. with `source ~/.opentox/opentox-ui.sh`), so any newly started shell will be configured. 
3. Use the system, in particular by starting webservices (see below). For `bash` users, a set of tools (called `ot-tools`) is available.
4. To uninstall, simply remove the line from the startup file, that's all. To save disc space also remove directory `OT_PREFIX`. To remove also your configuration, remove `~/.opentox`.

<br>
<br>
<hr>
<br>
<br>

# Installation

Installation (`development` at the point of writing this = 22/08/2012) is tested with Debian 6.0.5 and Ubuntu 11.04. Installer is available at [github](https://github.com/opentox/install).

Before starting the installation please check if "sudo" is available (e.g. sudo ls), required for base packages.

    # Install git
    sudo apt-get install git 

Please add your ssh key at github via https://github.com/. You may add your name/email address to the local git config.

Download, configure and execute the installer. 

    git clone git@github.com:opentox/install.git $HOME/install
    cd $HOME/install
    git checkout development
    # First check config.sh with your favorite editor
    ./install

This installation script will prepare your system for the use of `opentox` web services, including installing necessary dependencies, `opentox` config, `raptor`, `rasqal`, `rbenv`, `ruby` and `4store`. 

# Install opentox services

Load environment (general)

    . $HOME/.opentox/config/install/config.sh
    . $OT_PREFIX/install/utils.sh
    
Load environment (with bash):

    . $HOME/.opentox/ot-tools.sh
    otconfig

Next opentox-client and opentox-server will be downloaded and installed.
    
    for f in opentox-client opentox-server; do 
      git clone "git@github.com:opentox/$f.git" $OT_PREFIX/$f
      cd $OT_PREFIX/$f
      git checkout development 2>/dev/null
      git checkout migration 2>/dev/null
    done 
    cd $OT_PREFIX/opentox-client/bin 
    ./opentox-client-install 
    cd $OT_PREFIX/opentox-server/bin
    ./opentox-server-install

OpenTox services(algorithm compound dataset feature task opentox-test) and tests will be downloaded and installed. 

    for f in algorithm compound dataset feature model task opentox-test; do
      git clone "git@github.com:opentox/$f.git" $OT_PREFIX/$f
      cd $OT_PREFIX/$f
      git checkout development 2>/dev/null
      git checkout migration 2>/dev/null
      if [ -f $OT_PREFIX/$f/bin/$f-install ]; then
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

Start all services and the 4store backend with:

    otstart all 

Kill all services and the 4store backend with:
    
    otkill all

It is possible to manage single services (e.g. `otstart dataset` to run dataset web service). 

# Access web services

The default port setting for the web services and the 4store backend,  (see config files `~/.opentox/config/*.rb`):

| Service | URI | 
| ------ | ------ |
| --------------------  | ------------------------------------- |
| Algorithm | localhost:8081/algorithm | 
| Compound | localhost:8082/compound |
| Dataset | localhost:8083/dataset |
| Feature | localhost:8084/feature |
| Model | localhost:8085/model |
| Task | localhost:8086/task |
| Validation | localhost:8087/validation |
| 4store | localhost:9088/ |

NOTE: Only installed service URIs are set in the config files. Model and validation service are not available yet. 

# Testing services

    cd $OT_PREFIX/opentox-test
    otconfig
    ruby opentox-client.rb
    ruby test/dataset-large.rb


The tests take some time and finish with a short report:

    Finished tests in 44.690815s, 0.9622 tests/s, 4.4528 assertions/s.
    43 tests, 199 assertions, 1 failures, 0 errors, 0 skips

To test one specific service, run its script (`ruby [service_name].rb`) located in `$OT_PREFIX/opentox-test/test`.
