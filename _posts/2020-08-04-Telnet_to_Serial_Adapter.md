---
layout: post
title: Telnet to Serial Adapter
subtitle: Using Telnet to log in to headless SBCs
tags: [ESP-01,esp8266,telnet,serial]
comments: false
---

This post documents a small project that I find useful when one of my headless SBCs will no longer let me ssh into it.

# Telnet to Serial

The code was derived from Arduino Examples->ESP8266WiFi->WiFiTelnetToSerial.

Code is running on an ESP-01.

I use this project to attach to headless Raspberry Pi's and Odroid SBC's when I need to troubleshoot and I can't ssh into them.
I swapped only the TX pin. This ensures that ESP-01 bootup characters do not get injected into the Serial TX which would honk up the login.

You can find the repo/code [here](https://github.com/larsenkb/telnet2serial)

## Pin connections

I use jumper wires to connect the ESP-01 to the SBC serial port.

| ESP-01 | RPi | Func |
| :-----: | :---: | :----: |
| 1 | 6 | GND |
| 3 | 8 | TX |
| 7 | 10 | RX |
| 8 | 1 | 3v3 |


It might help to switch to character mode once you have stared up the telnet program.

^]

telnet> mode character

...proceed with telnet session

^]

telnet> quit
