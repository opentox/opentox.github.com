---
layout: post
title: "Installer issues"
description: ""
category: Installation
tags: [Ubuntu, Debian, Setup]
---
{% include JB/setup %}

Installer Issues for different versions of Ubuntu and Debian

# Ubuntu 12.04 (Precise)

Installation (`development` at the point of writing this = 25/05/2012) works fine on Ubuntu 12.04 apart from one issue: The GSL ruby gem is not compatible with libgsl0-dev version 1.15+dfsg-1. It is necessary to run version 1.14+dfsg-1. 

Proceed as follows **before installing**.:

    sudo aptitude remove libgsl0ldbl libgsl0-dev # have to accept any dependency removals such as inkscape
    curl -L http://launchpadlibrarian.net/48177999/libgsl0-dev_1.14%2Bdfsg-1_amd64.deb > libgsl0-dev_1.14+dfsg-1_amd64.deb
    curl -L http://launchpadlibrarian.net/48177998/libgsl0ldbl_1.14%2Bdfsg-1_amd64.deb > libgsl0ldbl_1.14+dfsg-1_amd64.deb
    sudo dpkg -i libgsl0-dev_1.14+dfsg-1_amd64.deb
    sudo dpkg -i libgsl0ldbl_1.14+dfsg-1_amd64.deb

In the above, adjust your architecture to `i386`, if necessary (please use 64 bit software where possible).

After the downgrade, do not forget to [pin](https://help.ubuntu.com/community/PinningHowto) both packages to the downgrade versions, otherwise Ubuntu will try to upgrade them immediately:

    sudo -s
    echo libgsl0-dev hold | dpkg --set-selections
    echo libgsl0ldbl hold | dpkg --set-selections

Recommendation: Do nothing more about it. If possible, use Ubuntu 11.10 (oneiric) or Debian 6.0 (squeeze), which both have version 1.14 of libgsl.

# Could not get fminer working with ruby-enterprise-1.8.7-2012.02

On one system (Ubuntu 10.04.3 LTS) we could compile the fminer libraries and ruby bindings, but using fminer via ruby produced segmentation faults. Moving back to ruby-enterprise-1.8.7-2011.03 (configure in config.sh) solved the issue.
