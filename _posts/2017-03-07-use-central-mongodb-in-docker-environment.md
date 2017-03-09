---
layout: post
title: "Use a central mongodb in docker environment for differrent services"
description: ""
category: Installation
tags: [Docker, Mongodb, Database]
---
{% include JB/setup %}


**download and run the mongodb**

    docker pull mongo
    docker run --name central-mongo -d mongo

**you can define the storage engine by name**

    docker run --name central-mongo -d mongo --storageEngine wiredTiger

**start your service and link the mongodb to it**

    docker run --name my-service --link central-mongo:mongodb -d insilicotox/my-service

**enter your service container and check the central mongodb network adress**

    docker exec -ti my-service bash
    cat /etc/hosts  -> 172.17.x.x mongodb
    env|grep MONGODB  -> MongoPort = tcp://172.17.x.x:27017

**use this port for your mongo commands e.g.**

    `mongorestore --host 172.17.x.x`
