---
layout: post
title: "Install opentox development environment"
description: ""
category: Installation
tags: [Deployment, Ubuntu, Debian, Setup]
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
- Encapsulated (everything installed in `OT_PREFIX`)
- Logged (all non-elementary actions are logged)

Conceptual approach:

1. Configure the installer in config.sh, then run `install`. 
2. Configure the system by adding a line to the startup file of your favorite shell (e.g. BASH with the file `~/.bashrc`) to read in `~/.opentox/opentox-ui.sh` (e.g. with `source ~/.opentox/opentox-ui.sh`), so any newly started shell will be configured. 
3. Use the system, in particular by starting webservices (see below). For BASH users, a set of tools (ot-tools) is available.
4. To uninstall, simply remove the line from the startup file. To save disc space also remove directory `OT_PREFIX` (not recommended). To remove also the configuration, remove `~/.opentox` (not recommended).

<br>
<br>
<hr>
<br>
<br>

# Installation

Installation (`development` at the point of writing this = 08/2014) tested with Debian 7.6 (wheezy), kernel 3.2.0-4-amd64, GNOME 3.4.2. Installer available at [github](https://github.com/opentox/install).

Check you can run "sudo" commands (e.g. `sudo ls`), which is required for base packages. Install git:

    # Install git
    sudo apt-get install git 

Download, configure and execute the installer (You might add your public key at `https://github.com`):

    git clone "https://github.com/opentox/install.git" $HOME/install
    cd $HOME/install
    git checkout development
    # First check config.sh with your favorite editor
    ./install silent

This will prepare the system for opentox web services, including dependencies. 

# Install OpenTox services

You can run the following steps with a script:

    bash $HOME/install/service_install.sh

Load environment (general):

    . $HOME/.opentox/config/install/config.sh
    . $OT_PREFIX/install/utils.sh
    
Load environment (with BASH):

    . $HOME/.opentox/ot-tools.sh
    otconfig

Download and install opentox-client and opentox-server:
    
    for f in opentox-client opentox-server; do 
      git clone "https://github.com/opentox/$f.git" $OT_PREFIX/$f
      cd $OT_PREFIX/$f
      git checkout development 2>/dev/null
    done 
    cd $OT_PREFIX/opentox-client/bin 
    ./opentox-client-install silent
    cd $OT_PREFIX/opentox-server/bin
    ./opentox-server-install silent

Download and install services and tests:

    for f in algorithm compound dataset feature model validation task opentox-test; do
      git clone "https://github.com/opentox/$f.git" $OT_PREFIX/$f
      cd $OT_PREFIX/$f
      git checkout development 2>/dev/null
      if [ -f $OT_PREFIX/$f/bin/$f-install ]; then
        cd $OT_PREFIX/$f/bin
        ./$f-install silent
      fi
    done
    notify

OpenTox compatible web services are now installed and configured.   
    

# Activate and use ot-tools for BASH 

NOTE: ot-tools require BASH.

Add ot-tools to your `.bashrc`:

    echo '. $HOME/.opentox/ot-tools.sh' >> ~/.bashrc
    # source .bashrc or restart shell

All ot-tools (`otconfig`, `ottail`, `otless`, `otstart`, `otkill`, `otupdate_repo`) are available in your shell. General usage: 

    [otstart|otkill|otupdate_repo] [all|algorithm|compound|dataset|feature|
    model|task|validation|4store]

`otconfig` is the only command not taking an argument. It sets the environment variables for all services. 

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
    ruby opentox-client-short.rb
    # Or a longer test script
    ruby opentox-client-all.rb


The tests take some time and finish with a short report:

    Finished tests in 375.726618s, 0.2741 runs/s, 14.1193 assertions/s.
    103 tests, 5305 assertions, 0 failures, 1 errors, 0 skips

To test one specific service, run its script (`ruby [service_name].rb`) in `$OT_PREFIX/opentox-test/test`.
