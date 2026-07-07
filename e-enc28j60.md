---
layout: ../../layouts/MarkdownPostLayout.astro
title: "Enc28j60 外接模块"
description: "Nano V3 Ethernet Shield - ENC28J60"
date: 2026-07-07
author: xiaobin
---
- [Nano V3 Ethernet Shield - ENC28J60](https://www.flyrobo.in/nano-v3-ethernet-shield)
> Purchased on February 12, 2020.

## [SAM4S-EK2](https://www.microchip.com/en-us/development-tool/atsam4s-ek)
- IDE: [iar V7.50.3.10751](https://tdtc-hrb.github.io/cnblogs/post/e-iar-project-setup)
- Advanced Software Framework (ASF): [V3.33.0.50](https://www.microchip.com/en-us/tools-resources/archives/avr-sam-mcus)
- ICE: j-link v8.0

### test
1. led & uart
```
xdk-asf-3.33.0\sam\applications\getting-started\sam4sd32c_sam4s_ek2
```
1) LED    
2) UART
```
Baud: 115200
```
2. TFT LCD
```
xdk-asf-3.33.0\sam\components\display\ili93xx\example\sam4sd32c_sam4s_ek2
```
1) On
```
JP13 Connect.
```

### web server
**无论是 USB 接口 供电，还是 圆形 DC 接口 供电 均无法工作!**

## Arduino Uno Rev 3e
- [blink](https://tdtc-hrb.github.io/cnblogs/post/e-avr8-blink) - Ok

### [web server](https://tdtc-hrb.github.io/cnblogs/post/e-avr8-web-server)
**供电1: USB接口 - 可以工作.**
***供电2: 圆形 DC 接口 - 无法工作!***

## IAR
- Current Use:
    V7.50.3
- [version history](http://supp.iar.com/Updates/?product=EWarm)

### j-link driver
```
IAR <=> J-Link:
   V7.50.3 - v5.10m
   V7.80.4 - v6.12f 
```

### down file: from [CSDN Blog](https://blog.csdn.net/weixin_38343172/article/details/84034840)
```
    CD-EWARM-6405-4275
    EWARM-CD-7501-10273.exe
    EWARM-CD-7503-10751.exe
```
for example:
- http://files.iar.com/ftp/pub/box/EWARM-CD-7602-11350.exe
- http://files.iar.com/ftp/pub/box/EWARM-CD-7804-12495.exe
