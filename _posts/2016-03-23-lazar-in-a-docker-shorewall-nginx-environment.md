---
layout: post
title: "Lazar in a Docker Shorewall Nginx Environment"
description: ""
category: Installation
tags: [Setup, Nginx, Docker, Shorewall, Lazar]
---
{% include JB/setup %}
**This post explains how to setup Lazar Docker image in a  Shorewall Nginx Environment with a static IP**

Docker creates its own virtual interface **docker0** with a network in a private subnet (in a random 172.17.x.x net). On container start it selects a random IP (e.g. 172.17.0.1) for the container.

In this example we want to run a lazar docker container with a static IP in a 10.0.1.x subnet. The lazar prediction application will be available with the lazar-gui at mysub.mydomain.mytld on port 80 for webbrowser. 
Shorewall is used as a firewall and to port-forward ssh direct into the container. Nginx at the host machine delivers HTTP access via FQDN to the GUI.    
    
This article summarizes several documentations, blog entries and stackoverflow discussions (all linked in the text) together with lazar setup. 

Please keep in mind: **Always check that your shorewall settings do not lock you out** of your own machine (ssh ports). Always ensure login before you close the last open ssh session.        

## Setup your own docker subnet

restart docker when changing network settings
    
    sudo systemctl stop docker
    sudo systemctl start docker

create a docker bridge (br0) with subnet 10.0.1.1/24 (see also https://docs.docker.com/engine/reference/commandline/network_create/) 

    docker network create --subnet=10.0.1.1/24  --gateway=10.0.1.254 br0

check with:

    docker network ls
    >>>
    NETWORK ID          NAME                DRIVER
    6c30cce12345        br0                 bridge              
    0fd4123450fc        bridge              bridge              
    e3a931234593        none                null                
    e311234523dc        host                host


## Download and setup lazar

download the docker lazar image 

    docker pull insilicotox/lazar-public-data

run docker image with a static IP

    docker run -p 8088:8088 --net br0 --ip 10.0.1.101 -itd insilicotox/lazar-public-data:v3

list containers to find out CONTAINER_ID

    docker ps -a

start container with 

    docker start <CONTAINER_ID>

go into the container to start the database and lazar-gui application (see also: https://hub.docker.com/r/insilicotox/lazar-public-data/)

    docker exec -ti CONTAINER_ID bash

    ist@CONTAINER_ID:~$  sudo /etc/init.d/mongod start
    ist@CONTAINER_ID:~$  cd lazar-gui
    ist@CONTAINER_ID:~$  unicorn -p 8088 -D
    ist@CONTAINER_ID:~$  exit

## Shorewall Setup
get the name of the new bridge

    ip addr
    >>>
    ...
    34: br-6c30cceb5756: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc noqueue state UP group default 
      link/ether 02:42:6f:42:d2:8e brd ff:ff:ff:ff:ff:ff
      inet 10.0.1.254/24 scope global br-6c30cceb5756
         valid_lft forever preferred_lft forever
      inet6 fe80::42:6fff:fe42:d28e/64 scope link 
         valid_lft forever preferred_lft forever

name of the network bridge in this example is **br-6c30cceb5756** 

for a clean restart of shorewall do

    sudo shorewall stop
    sudo shorewall clear
    sudo shorewall start


### Shorewall versions < 5.0.6
see http://shorewall.net/Docker.html

add hooks to /etc/shorewall/init /etc/shorewall/stop and /etc/shorewall/start to keep iptables settings from docker as described at:
http://blog.discourse.org/2015/11/shorewalldocker-two-great-tastes-that-taste-great-together/

for ***/etc/shorewall/init*** and ***/etc/shorewall/stop***

    if iptables -t nat -L DOCKER >/dev/null 2>&1; then
        echo '*nat' >/etc/shorewall/docker_rules
        iptables -t nat -S DOCKER >>/etc/shorewall/docker_rules
        iptables -t nat -S POSTROUTING >>/etc/shorewall/docker_rules
        echo "COMMIT" >>/etc/shorewall/docker_rules
    
        echo '*filter' >>/etc/shorewall/docker_rules
        iptables -S DOCKER >> /etc/shorewall/docker_rules
        echo "COMMIT" >>/etc/shorewall/docker_rules
    fi

for ***/etc/shorewall/start***

    if [ -f /etc/shorewall/docker_rules ]; then
        iptables-restore -n </etc/shorewall/docker_rules
        run_iptables -t nat -I PREROUTING -m addrtype --dst-type LOCAL -j DOCKER
        run_iptables -t nat -I OUTPUT ! -d 127.0.0.0/8 -m addrtype --dst-type LOCAL -j DOCKER
        run_iptables -I FORWARD -o docker0 -j DOCKER
        run_iptables -I FORWARD -o docker0 -m conntrack --ctstate RELATED,ESTABLISHED -j ACCEPT
        run_iptables -I FORWARD -i docker0 ! -o docker0 -j ACCEPT
        run_iptables -I FORWARD -i docker0 -o docker0 -j ACCEPT
    
        rm -f /etc/shorewall/docker_rules
    fi



#### shorewall configuration files

add the bridge to shorewall interfaces ('br0' is an example name, name can be at most 5 characters long)  : ***/etc/shorewall/interfaces***

    br0       br-6c30cceb5756 detect          routeback,bridge

add new line for bridge in ***/etc/shorewall/zones***

    br0      ipv4

ssh into the container add a forwarding from port 22101 on host to port 22 on container at:  ***/etc/shorewall/rules***    
You might to start ssh daemon inside the container with `sudo /etc/init.d/ssh restart`
 
    DNAT            net             br0:10.0.1.101:22       tcp     22101


add policies to ***/etc/shorewall/policy*** (do not place behind "all             all             REJECT          info")

    br0             br0             ACCEPT
    br0             net             ACCEPT
    br0             fw              ACCEPT
    fw              br0             ACCEPT
    br0             all             ACCEPT


## Nginx Setup

set proxy in nginx to give port 8088 to port 80 on webserver 

    server {
        listen          80;
        server_name mysub.mydomain.mytld;
        client_max_body_size 5000m;
        proxy_read_timeout 600;
        location / {
              proxy_pass http://10.0.1.101:8088;
              proxy_set_header   Host $host;
         }
    }

