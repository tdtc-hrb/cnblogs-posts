---
title: "web server - Enc28j60"
description: "支持 AS 7.0"
date: 2026-08-18T06:08:08+08:00
---
**UIP Ethernet工程 导入 到 Atmel Studio(Microchip Studio) 无法编译**

**Ethernet enc 没有这个问题**

- Arduino [V1.8.19](https://www.arduino.cc/en/software)
- [Ethernet enc v2.x](https://github.com/Networking-for-Arduino/EthernetENC)

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

**供电: 必须使用USB接口.**
***使用 圆形 DC 接口 无法工作!***

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

### Arduin IDE v1.8.19
[v2.0.5](https://github.com/Networking-for-Arduino/EthernetENC/archive/refs/tags/2.0.5.zip)
```
Sketch -> Include Library
Add .ZIP Library
```

## Code
Ethernet_Demo_1.ino
```c
#include <SPI.h>
#include <EthernetENC.h>

// Enter a MAC address and IP address for your controller below.
// The IP address will be dependent on your local network:
byte mac[] = {
  0xDE, 0xAD, 0xBE, 0xEF, 0xFE, 0xED
};
IPAddress ip(192, 168, 1, 177);

// Initialize the Ethernet server library
// with the IP address and port you want to use
// (port 80 is default for HTTP):
EthernetServer server(80);

void setup() {
  // You can use Ethernet.init(pin) to configure the CS pin
  //Ethernet.init(10);  // Most Arduino shields
  //Ethernet.init(5);   // MKR ETH shield
  //Ethernet.init(0);   // Teensy 2.0
  //Ethernet.init(20);  // Teensy++ 2.0
  //Ethernet.init(15);  // ESP8266 with Adafruit Featherwing Ethernet
  //Ethernet.init(33);  // ESP32 with Adafruit Featherwing Ethernet

  // Open serial communications and wait for port to open:
  Serial.begin(9600);
  while (!Serial) {
    ; // wait for serial port to connect. Needed for native USB port only
  }
  Serial.println("Ethernet WebServer Example");

  // start the Ethernet connection and the server:
  Ethernet.begin(mac, ip);

  // Check for Ethernet hardware present
  if (Ethernet.hardwareStatus() == EthernetNoHardware) {
    Serial.println("Ethernet shield was not found.  Sorry, can't run without hardware. :(");
    while (true) {
      delay(1); // do nothing, no point running without Ethernet hardware
    }
  }
  if (Ethernet.linkStatus() == LinkOFF) {
    Serial.println("Ethernet cable is not connected.");
  }

  // start the server
  server.begin();
  Serial.print("server is at ");
  Serial.println(Ethernet.localIP());
}

void loop() {
  // listen for incoming clients
  EthernetClient client = server.available();
  if (client) {
    Serial.println("new client");
    // an http request ends with a blank line
    boolean currentLineIsBlank = true;
    while (client.connected()) {
      if (client.available()) {
        char c = client.read();
        Serial.write(c);
        // if you've gotten to the end of the line (received a newline
        // character) and the line is blank, the http request has ended,
        // so you can send a reply
        if (c == '\n' && currentLineIsBlank) {
          // send a standard http response header
          client.println("HTTP/1.1 200 OK");
          client.println("Content-Type: text/html");
          client.println("Connection: close");  // the connection will be closed after completion of the response
          client.println("Refresh: 5");  // refresh the page automatically every 5 sec
          client.println();
          client.println("<!DOCTYPE HTML>");
          client.println("<html>");
          // output the value of each analog input pin
          for (int analogChannel = 0; analogChannel < 6; analogChannel++) {
            int sensorReading = analogRead(analogChannel);
            client.print("analog input ");
            client.print(analogChannel);
            client.print(" is ");
            client.print(sensorReading);
            client.println("<br />");
          }
          client.println("</html>");
          break;
        }
        if (c == '\n') {
          // you're starting a new line
          currentLineIsBlank = true;
        } else if (c != '\r') {
          // you've gotten a character on the current line
          currentLineIsBlank = false;
        }
      }
    }
    // give the web browser time to receive the data
    delay(1);
    // close the connection:
    client.stop();
    Serial.println("client disconnected");
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
