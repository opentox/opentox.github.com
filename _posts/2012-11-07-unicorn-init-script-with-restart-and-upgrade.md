---
layout: post
title: "Unicorn Init Script with restart and upgrade"
description: ""
category: Installation
tags: [Setup]
---
{% include JB/setup %}

##Restarting Seamlessly Unicorn Master Processes

Therefore we use a `before_fork` Proc in the unicorn configuration file.
The `before_fork` Proc object will be called by the master process before forking each worker. 
In our configuration file it kills the master process that belongs to the .oldbin PID. 

Sending the USR2 signal with an init.d script forks the unicorn master process and gives the former process an old pid. 
The process with the old pid gets a QUIT signal from the before_fork code when the new master process starts a new worker process. 
The old master and its workers are able to finish their jobs and end afterwards.


##unicorn configuration file in the application directory
can go to application directory e.g.: `/home/USER/opentox-ruby/APPLICATION/unicorn.rb`
 
<script src="https://gist.github.com/4037704.js"> </script>


##  init.d script to start|stop|restart|force-stop|upgrade the webservice
Script is `/etc/init.d/investigation` . 

<script src="https://gist.github.com/4037667.js"> </script>


## Embed it in a Debian System
* To make it work on system startup add the init.d script (start and stop) to the runlevels defined in the head of the script (Default-Start and Default-Stop) with `sudo update-rc.d APPLICATION defaults` .
* To enable logrotation for the errorlog file add `/etc/logrotate.d/investigation` to the logrotate.d directory with the content:

      /home/investigation/.opentox/log/unicorn-investigation.stderr.log {
        copytruncate
        daily
        rotate 150
        missingok
        create 640 investigation investigation
      }
* To monitor the application with monit add the configuration file: `/etc/monit/conf.d/investigation` to make monit check if the pid-file exists. If not monit starts the service.

      check process investigation with pidfile /home/investigation/.opentox/pid/unicorn-investigation.pid
        stop program = "/etc/init.d/investigation stop"
        start program = "/etc/init.d/investigation start"
