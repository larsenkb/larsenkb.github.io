---
layout: post
title: Local Blynk Server
subtitle: A local blynk server for my home automation
tags: [mqtt,433Mhz,esp8266,blynk]
comments: false
---

In this post I am adding more features to my MQTT \<--\> 433MHz Bridge. I want to add a local Blynk server so that I can control my Zap outlets from the Blynk app and from Home Assistant, etc.

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
$ docker run --name blynk-server -v ~/blynk-server-data:/data -p 8440:8440 -p 8080:8080 -p 9443:9443 -d blynk
~~~
That should create and start a blynk-server running in a docker container.
I still need to figure out how to make it start running on power cycle.
To manually start it running again:
~~~
$ docker container start blynk-server
~~~

Here is the file run.sh:
~~~
#!/bin/bash

set -e

echo "hardware.mqtt.port=${HARDWARE_MQTT_PORT}
hardware.ssl.port=${HARDWARE_MQTT_PORT_SSL}
http.port=${HTTP_PORT}
force.port.80.for.csv=${FORCE_PORT_80_FOR_CSV}
force.port.80.for.redirect=${FORCE_PORT_80_FOR_REDIRECT}
https.port=${HTTPS_PORT}
server.ssl.cert=${SERVER_SSL_CERT}
server.ssl.key=${SERVER_SSL_KEY}
server.ssl.key.pass=${SERVER_SSL_KEY_PASS}
logs.folder=./logs
log.level=${LOG_LEVEL}
user.devices.limit=${USER_DEVICES_LIMIT}
user.tags.limit=${USER_TAGS_LIMIT}
user.dashboard.max.limit=${USER_DASHBOARD_MAX_LIMIT}
user.widget.max.size.limit=${USER_WIDGET_MAX_SIZE_LIMIT}
user.message.quota.limit=${USER_MESSAGE_QUOTA_LIMIT}
notifications.queue.limit=${NOTIFICATIONS_QUEUE_LIMIT}
blocking.processor.thread.pool.limit=${BLOCKING_PROCESSOR_THREAD_POOL_LIMIT}
notifications.frequency.user.quota.limit=${NOTIFICATIONS_FREQUENCY_USER_QUOTA_LIMIT}
webhooks.frequency.user.quota.limit=${WEBHOOKS_FREQUENCY_USER_QUOTA_LIMIT}
webhooks.response.size.limit=${WEBHOOKS_RESPONSE_SIZE_LIMIT}
user.profile.max.size=${USER_PROFILE_MAX_SIZE}
terminal.strings.pool.size=${TERMINAL_STRINGS_POOL_SIZE}
map.strings.pool.size=${MAP_STRINGS_POOL_SIZE}
lcd.strings.pool.size=${LCD_STRINGS_POOL_SIZE}
table.rows.pool.size=${TABLE_ROWS_POOL_SIZE}
profile.save.worker.period=${PROFILE_SAVE_WORKER_PERIOD}
stats.print.worker.period=${STATS_PRINT_WORKER_PERIOD}
web.request.max.size=${WEB_REQUEST_MAX_SIZE}
csv.export.data.points.max=${CSV_EXPORT_DATA_POINTS_MAX}
hard.socket.idle.timeout=${HARD_SOCKET_IDLE_TIMEOUT}
enable.db=${ENABLE_DB}
enable.raw.db.data.store=${ENABLE_RAW_DB_DATA_STORE}
async.logger.ring.buffer.size=${ASYNC_LOGGER_RING_BUFFER_SIZE}
allow.reading.widget.without.active.app=${ALLOW_READING_WIDGET_WITHOUT_ACTIVE_APP}
allow.store.ip=${ALLOW_STORE_IP}
initial.energy=${INITIAL_ENERGY}
admin.rootPath=${ADMIN_ROOT_PATH}
restore.host=${RESTORE_HOST}
product.name=${PRODUCT_NAME}
admin.email=${ADMIN_EMAIL}
admin.pass=${ADMIN_PASS}
" >> /data/server.properties

java -jar /blynk/server.jar -dataFolder /data -serverConfig /data/server.properties
~~~

And here is the file 'Dockerfile':
~~~
FROM ubuntu
MAINTAINER Florian Mauduit <f@lf.je>

############################################################
# Install OpenJDK
RUN apt update && apt install -y openjdk-11-jdk libxrender1 maven
RUN apt install -y curl

## Server Port
ENV BLYNK_SERVER_VERSION=0.41.12

RUN mkdir /blynk
RUN curl -L https://github.com/blynkkk/blynk-server/releases/download/v${BLYNK_SERVER_VERSION}/server-${BLYNK_SERVER_VERSION}.jar > /blynk/server.jar

RUN mkdir /data
VOLUME ["/data"]

RUN mkdir -p /usr/local/bin
ADD ./bin /usr/local/bin
RUN chmod +x /usr/local/bin/*.sh

EXPOSE ${HARDWARE_MQTT_PORT} ${HARDWARE_MQTT_PORT_SSL} ${HTTP_PORT} ${HTTPS_PORT}

WORKDIR /data

ENV HARDWARE_MQTT_PORT=8440 \
    HTTP_PORT=8080 \
    HTTPS_PORT=9443 \
    LOG_LEVEL=trace \
    FORCE_PORT_80_FOR_CSV=false \
    FORCE_PORT_80_FOR_REDIRECT=true \
    USER_DEVICES_LIMIT=50 \
    USER_TAGS_LIMIT=100 \
    USER_DASHBOARD_MAX_LIMIT=100 \
    USER_WIDGET_MAX_SIZE_LIMIT=20 \
    USER_MESSAGE_QUOTA_LIMIT=100 \
    NOTIFICATIONS_QUEUE_LIMIT=2000 \
    BLOCKING_PROCESSOR_THREAD_POOL_LIMIT=6 \
    NOTIFICATIONS_FREQUENCY_USER_QUOTA_LIMIT=5 \
    WEBHOOKS_FREQUENCY_USER_QUOTA_LIMIT=1000 \
    WEBHOOKS_RESPONSE_SIZE_LIMIT=96 \
    USER_PROFILE_MAX_SIZE=256 \
    TERMINAL_STRINGS_POOL_SIZE=25 \
    MAP_STRINGS_POOL_SIZE=25 \
    LCD_STRINGS_POOL_SIZE=6 \
    TABLE_ROWS_POOL_SIZE=100 \
    PROFILE_SAVE_WORKER_PERIOD=60000 \
    STATS_PRINT_WORKER_PERIOD=60000 \
    WEB_REQUEST_MAX_SIZE=524288 \
    CSV_EXPORT_DATA_POINTS_MAX=43200 \
    HARD_SOCKET_IDLE_TIMEOUT=10 \
    ADMIN_ROOT_PATH=/admin \
    PRODUCT_NAME=Blynk \
    RESTORE_HOST=blynk-cloud.com \
    ALLOW_STORE_IP=true \
    ALLOW_READING_WIDGET_WITHOUT_ACTIVE_APP=false \
    ASYNC_LOGGER_RING_BUFFER_SIZE=2048 \
    ENABLE_DB=false \
    ENABLE_RAW_DB_DATA_STORE=false \
    INITIAL_ENERGY=100000 \
    ADMIN_EMAIL=xxxx@xxxx.com \
    ADMIN_PASS=xxxx

ENTRYPOINT ["/usr/local/bin/run.sh"]
~~~
