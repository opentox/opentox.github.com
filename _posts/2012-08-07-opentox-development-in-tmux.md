---
layout: post
title: "Opentox development in tmux"
description: ""
category: development
tags: [development]
---
{% include JB/setup %}

The following script starts each opentox service in a separate tmux window and provides separate windows for editing and testing - kind of a IDE for opentox and very useful for debugging.


    #!/bin/sh

    # kill/restart services
    killall unicorn
    pkill 4s-httpd
    pkill 4s-backend
    sudo /etc/rc.d/avahi-daemon restart
    4s-backend opentox

    cd ~/opentox
    # create a new session and detach from it
    tmux new-session -d  -s opentox -n  '4s-httpd -p 8080 -D opentox'

    # tmux configuration
    tmux set set-remain-on-exit on
    tmux bind r respawn-window

    # start services
    cd ~/opentox/task
    pwd && git pull
    tmux new-window -n 'task' 'unicorn -p 8081'
    cd ~/opentox/feature
    pwd && git pull
    tmux new-window -n 'feature' 'unicorn -p 8082'
    cd ~/opentox/dataset
    pwd && git pull
    tmux new-window -n 'dataset' 'unicorn -p 8083'
    cd ~/opentox/toxbank-investigation
    pwd && git pull
    tmux new-window -n 'investigation' 'unicorn -p 8084'
    cd ~/opentox/model
    pwd && git pull
    tmux new-window -n 'model' 'unicorn -p 8085'
    cd ~/opentox/opentox-test
    pwd && git pull
    tmux new-window -n 'test' 
    cd ~/opentox
    tmux new-window -n 'work' 
    tmux select-window -t :6
    tmux -2 attach-session 

Tip: To restart a service move to the corresponding window (`Ctrl-b $window_id`), kill the service (`Ctrl-c`) and respawn the window (`Ctrl-b r`).
