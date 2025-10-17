---
title: "arduino 开发环境"
description: "arduino uno"
date: 2021-03-06T05:08:08+08:00
---
  使用跑马灯来验证.
# Pre IDE
- [Arduino CC(.zip文件)](https://www.arduino.cc/en/software)
- [Atmel Studio 7](https://www.microchip.com/en-us/development-tools-tools-and-software/microchip-studio-for-avr-and-sam-devices)

# 编码
我们使用使用AS7是为了使用它的智能提示和方便查看函数原型.
## 建立工程 - Arduino
文件->示例->01.Basics->Blink

## 建立工程 - AS7
New->Project    
"Create Project from Arduino Sketch"
![Create Project from Arduino Sketch](https://gitee.com/xiaobin80/cnblogs/raw/master/images/20210306045329.png)
设置Arduino sketch属性: Sketch File、Arduino IDE、Board、Device
![设置Arduino sketch](https://gitee.com/xiaobin80/cnblogs/raw/master/images/20210306045520.png)



# 烧写
把代码copy回arduino ide
```c
/*
  Blink
  Turns on an LED on for one second, then off for one second, repeatedly.

  Most Arduinos have an on-board LED you can control. On the UNO, MEGA and ZERO
  it is attached to digital pin 13, on MKR1000 on pin 6. LED_BUILTIN takes care
  of use the correct LED pin whatever is the board used.
  If you want to know what pin the on-board LED is connected to on your Arduino model, check
  the Technical Specs of your board  at https://www.arduino.cc/en/Main/Products

  This example code is in the public domain.

  modified 8 May 2014
  by Scott Fitzgerald

  modified 2 Sep 2016
  by Arturo Guadalupi

  modified 6 Mar 2021
  by tdtc
*/
int led1 = 2; // pin2
int led2 = 3; // pin3
int led3 = 4; // pin4

// the setup function runs once when you press reset or power the board
void setup() {
  // initialize digital pin LED_BUILTIN as an output.
  pinMode(led1, OUTPUT);
  pinMode(led2, OUTPUT);
  pinMode(led3, OUTPUT);
}

// the loop function runs over and over again forever
void loop() {
  digitalWrite(led1, HIGH);   // turn the LED on (HIGH is the voltage level)
  delay(1000);                       // wait for a second
  digitalWrite(led1, LOW);    // turn the LED off by making the voltage LOW
  delay(1000);                       // wait for a second
  digitalWrite(led2, HIGH);
  delay(1000);
  digitalWrite(led2, LOW);
  delay(1000);
  digitalWrite(led3, HIGH);
  delay(1000);
  digitalWrite(led3, LOW);
  delay(1000);
}
```
step 1: 项目->验证/编译    
step 2: 项目->上传    
在上传前,一定要查看"工具"中"端口"信息是否正确!

# Ref
- [Debugging for the Arduino UNO with Atmel Studio](https://wolles-elektronikkiste.de/en/debugging-for-the-arduino-uno-with-atmel-studio)
