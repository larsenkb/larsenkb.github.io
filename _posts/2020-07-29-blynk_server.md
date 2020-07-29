---
layout: post
title: Local Blynk Server
subtitle: A local blynk server for my home automation
tags: [mqtt,433Mhz,esp8266,blynk]
comments: false
---

In this post I am adding more features to my MQTT <--> 433MHz Bridge. I want to add a local Blynk server so that I can control my Zap outlets from the Blynk app and from Home Assistant, etc.

This small post documents the steps taken to setup the blynk server on my local ODROID-XU4 server called 'omv'.
This SBC also runs Home Assistant in docker containers and runs a MQTT broker.
I will setup the blynk server in a docker container on 'omv'.

After logging into 'omv':
~~~
$ cd
$ git clone https://github.com/blynkkk/blynk-server.git
$ cd blynk-server/server/Docker
$ mv Dockerfile Dockerfile.orig
$ rcp granpa:~/downloads/Dockerfile .
$ rcp granpa:~/downloads/run.sh .
$ docker build -t blynk .
$ cd
$ mkdir blynk-server-data
$ docker run --name blynk-server -v ~/blunk-server-data:/data -p 8440:8440 -p 8080:8080 -p 9443:9443 -d blynk
~~~
That should create and start a blynk-server running in a docker container.
I still need to figure out how to make it start running on power cycle.
To manually start it runninng again:
~~~
$ docker container start blynk-server
~~~

