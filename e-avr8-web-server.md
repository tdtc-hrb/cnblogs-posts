---
title: "web server"
description: "使用Enc28j60模块"
date: 2023-03-28T01:08:08+08:00
---
created date： 2021-03-10    
modified date： 
```
2023-03-25
```

- IDE    
Arduino [V1.8.19](https://www.arduino.cc/en/software)
- Library    
[UIP Ethernet v2.x](https://github.com/UIPEthernet/UIPEthernet/releases)

# 接线
|Arduino Uno|Enc28j60|
|-|-|
|Pin 10|CS|
|Pin 11|SI|
|Pin 12|SO|
|Pin 13|SCK|
|Reset|RST<sup>*</sup>|
|+3v3|Vcc|
|GND<sup>*</sup>|GND|

![spi connect](https://gitee.com/xiaobin80/cnblogs/raw/master/images/Connection-Diagram.png)

## Enc28j60
INTN: 置空（不接）

*Rst接线: 注意 [Nano V3 Ethernet Shield](https://robotdyn.com/nano-v3-ethernet-shield-enc28j60.html) 接7号线柱，如下图所示：
![reset connect](https://gitee.com/xiaobin80/cnblogs/raw/master/images/NanoEthENC28J60-Shield.jpg)

## [Arduino Uno](https://content.arduino.cc/assets/UNO-TH_Rev3e_sch.pdf)
*GND接线:  5V旁边的GND.

供电: 必须使用USB接口.(使用电源口无法工作)

# 网络及PC驱动
两种方式：
- 平行线    
接路由器Lan口。

- 交叉线    
接PC的Lan口。

## Network
设置同一网段的IP地址。
- PC    
Control Panel -> Network and Internet -> Network Connections    
Ethernet -> Properties:
```
Internet Protocol Version 4(TCP/IPv4)
```
- Enc28J60    
IP and [MAC](https://www.browserling.com/tools/random-mac) 在源代码中更改.

### Router
不支持以下路由器:
```
Huawei AX2 Pro
```

## Driver
- Windows 10 Enterprise LTSC 2019 English(x64)    
No need to manually install drivers.

- Windows 7 SP1 简体中文专业版(x64)    
首先，解压缩Arduino IDE下的drivers\Old_Arduino_Drivers.zip；

然后，通过设备管理器-〉未知设备-〉“更新驱动程序软件”，即可安装完成。


# example
If there is a new board manager please upgrade it.
```
Tools -> Boards Manager
Arduino AVR Boards -> update
```
If there is a new EthernetENC library please update it.
```
Tools -> Library Manager
EthernetENC -> update
```

- Board    
Tools -> Arduino Uno

- Port    
Tools -> Arduino Uno(COM3)

- Programmer    
AVRISP mkII


## 添加UIPEthernet库
Download the ZIP file from github, and add it.

### Arduin IDE v1.8.13
> 项目-〉加载库-〉添加.zip库

[v2.0.9](https://github.com/UIPEthernet/UIPEthernet/archive/refs/tags/v2.0.9.zip)

### Arduin IDE v1.8.19
[v2.0.12](https://github.com/UIPEthernet/UIPEthernet/archive/refs/tags/v2.0.12.zip)
```
Sketch -> Include Library
Add .ZIP Library
```

## Code
Ethernet_Demo_2.ino
```c
/* Demo sketch for a simple web page
 *
 * Libs: UIPEthernet v2.0.9 ~ v2.0.12
 *       https://github.com/UIPEthernet/UIPEthernet/releases
 *
 * IDE: Arduino v1.8.13
 *      ~ 
 *      Arduino v1.8.19
 *
 * Ethernet: Enc28J60
 *
 */
#include "Arduino.h"
#include <UIPEthernet.h>

#ifndef BOARD_NAME
  #define BOARD_NAME    "AVR Mega"
#endif

int reqCount = 0;                // number of requests received

// Let's create our Web Server on port 1000 (use port 80 for standard browsing)
// This means your browser request MUST append :1000 the URL. Use port 80 if you
// don't want to do this.
EthernetServer server = EthernetServer(1000);

// ------------------------------------------------------------------------------
// SETUP     SETUP     SETUP     SETUP     SETUP     SETUP     SETUP     SETUP
// ------------------------------------------------------------------------------
void setup() {
	// Serial Monitor aka Debugging Window
	Serial.begin(9600);

	// You must assign a UNIQUE MAC address to the Ethernet module. Well, unique
	// in your home or work setup anyway. Set it and don't keep changing is as
	// your router is tracking it.
	uint8_t mac[6] = { 0x93, 0x9c, 0x03, 0xde, 0x3b, 0x22 };

	// Give your web server a STATIC address here so the browser can find it.
	// From a Windows command prompt, try and "ping" it to see whether you can
	// see it on the network.
/*
Microsoft Windows [Version 10.0.17763.4131]
(c) 2018 Microsoft Corporation. All rights reserved.

C:\Users\tdtc>ping 192.168.2.86

Pinging 192.168.2.86 with 32 bytes of data:
Reply from 192.168.2.86: bytes=32 time=479ms TTL=128
Reply from 192.168.2.86: bytes=32 time=2ms TTL=128
Reply from 192.168.2.86: bytes=32 time=2ms TTL=128
Reply from 192.168.2.86: bytes=32 time=2ms TTL=128

Ping statistics for 192.168.2.86:
    Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),
Approximate round trip times in milli-seconds:
    Minimum = 2ms, Maximum = 479ms, Average = 121ms
*/
	IPAddress myIP(192, 168, 2, 86);

	// All information set up, we can begin
	Ethernet.begin(mac, myIP);
	server.begin();

	// Just to prove that your server is running on your specified IP address
	Serial.print("IP Address: ");
	Serial.println(Ethernet.localIP());
}

void loop()
{
  // listen for incoming clients
  EthernetClient client = server.available();

  if (client)
  {
    Serial.println(F("New client"));
    // an http request ends with a blank line
    bool currentLineIsBlank = true;

    while (client.connected())
    {
      if (client.available())
      {
        char c = client.read();
        Serial.write(c);
        // if you've gotten to the end of the line (received a newline
        // character) and the line is blank, the http request has ended,
        // so you can send a reply
        if (c == '\n' && currentLineIsBlank)
        {
          Serial.println(F("Sending response"));

          // send a standard http response header
          // use \r\n instead of many println statements to speedup data send
          client.print(
            "HTTP/1.1 200 OK\r\n"
            "Content-Type: text/html\r\n"
            "Connection: close\r\n"  // the connection will be closed after completion of the response
            "Refresh: 20\r\n"        // refresh the page automatically every 20 sec
            "\r\n");
          client.print("<!DOCTYPE HTML>\r\n");
          client.print("<html>\r\n");
          client.print(String("<h2>Hello World from ") + BOARD_NAME + "!</h2>\r\n");
          client.print("Requests received: ");
          client.print(++reqCount);
          client.print("<br>\r\n");
          client.print("Analog input A0: ");
          client.print(analogRead(0));
          client.print("<br>\r\n");
          client.print("</html>\r\n");
          break;
        }

        if (c == '\n')
        {
          // you're starting a new line
          currentLineIsBlank = true;
        }
        else if (c != '\r')
        {
          // you've gotten a character on the current line
          currentLineIsBlank = false;
        }
      }
    }
    // give the web browser time to receive the data
    delay(10);

    // close the connection:
    client.stop();
    Serial.println(F("Client disconnected"));
  }
}
```

## Browser
使用浏览器进入http://192.168.0.86:1000

### Support
- IE    
支持交叉线和路由器网络
![IE11](https://github.com/tdtc-hrb/cnblogs/raw/master/images/NanoEthENC28J60-ie.png)

- Firefox    
两种（交叉网络和路由器网络）都不支持。

- Chrome    
> v111.0
需要连接到路由器。
![Chrome v111.0](https://github.com/tdtc-hrb/cnblogs/raw/master/images/NanoEthENC28J60-chrome.png)


# Ref
- [setup part](https://github.com/RalphBacon/Arduino_Ethernet_ENC28J60_W5100)
- [loop part](https://github.com/khoih-prog/EthernetWebServer/tree/master/examples/WebServer)
