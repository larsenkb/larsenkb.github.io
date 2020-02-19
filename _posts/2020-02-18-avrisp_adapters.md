---
layout: post
title: AVR Adapters
subtitle: Programming adapters I've built.
tags: [AVR,ISP]
comments: false
---

I've used a lot of different AVR programmers in the past including some home made ones:  
* PC parallel port (with & without 74xx244)
* PC parallel port (with 74xx125)
* PC serial port (with 74xx126)
* PC serial port with transistors & zeners
* USBtiny using ATtiny2313 and V-USB

I used perf board and point-to-point wiring on all those I made. Now that I need glasses, it is not as easy to build stuff.

I purchased a few different ISP programmers, too:  
* AVR Butterfly
* Atmel STK500
* Bus Pirate
* Sparkfun AVR programmer

Currently, I use the AVR USB ISP Version 2.0 programmers purchased from [AliExpress](https://www.aliexpress.com/item/33058820632.html?spm=a2g0o.productlist.0.0.7f7162baWPHDPv&algo_pvid=1870d67e-d175-4b41-84f5-6b9bed5020b9&algo_expid=1870d67e-d175-4b41-84f5-6b9bed5020b9-3&btsid=0ab50f6115820953717403273e475a&ws_ab_test=searchweb0_0,searchweb201602_,searchweb201603_). But this blog entry is mainly to document several of the programmer adapters/cables I've built.

All the programmers I've built and bought have a 10 pin (2x5) 0.1\" male header to expose the programmer pins.
I have salvaged the gray 10 pin (2x5) to DB-9 ribbon cable from old PCs to use as AVR programmer cables.
These cables usually only have 9 wires in them, the 10th wire is nc.
![Internal PC Serial Cable](../img/pc_serial.png){: .center-block :}

I cut off the DB-9 connector and attach female pins that mate with the standard 0.1\" header pins.
![AVR cable](../img/avr_cable.png){: .center-block :}

I use this cable directly with AVR modules that already have the 6-pin (2x3) male ISP header.
I plug the cable into the header pin by pin.
I use the same cable with proto boards usually in two different ways:  
1. plug a 6-pin right angle 0.1\" header into the proto board and the other end into the female pins of the cable. Then I wire from AVR IC to the 6-pin right angle header.
2. I insert male pins into the female pins of the cable which converts the cable from female to male. Then I plug each cable pin directly into the proto board at the IC pin location.

I've made a few variations of the 6-pin adapter:  
1. onde adapter has series resistors to help isolate the programmer from peripherals attached to the target AVR.
2. My latest adapter has a reset button, a 5V to 3.3V LDO, and a jumper to isolate ISP VCC from target VCC, in case target is already powered. The order of the pins on this adapter was dictated by my lofi project. I included the 3.3V LDO for my lofi project because even though the tiny84a can run from 5V, the attached NRF24l01+ can not. I wanted to ensure I didn't accidently put 5V on the ISP VCC pin. ![adapter front](../img/avr_adapter_front.png){: .center-block :} ![adapter back](../img/avr_adapter_back.png){: .center-block :}

The latest programmer adapter I've made is a pogo pin adapter for my lofi project that uses my designed PWBs. The ISP pads on the lofi board are on 0.05\" spaced pads in a 2x3 arrangment. The adapter from 2) above plugs into the clothes pin pogo adapter.

