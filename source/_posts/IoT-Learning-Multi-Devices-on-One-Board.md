---
title: 'IoT Learning: Multi Devices on One Board'
date: 2023-03-11 14:13:08
tags: [IoT, ESP32, Arduino, blinker]
description: 借助 ESP32 平台，接入小爱同学语音控制，完成宿舍灯光的控制。此次与 WoL 唤醒采用了同一个板子，需要特别注意 Blinker 多设备控制的方法。
abbrlink: Multi-Devices-on-One-Board
cover: /img/Multi-Devices-on-One-Board.png
categories:
---

# Part 1: 思路介绍

于 [WoL唤醒电脑](https://remoder.github.io/Turn-on-PC-by-WoL.html) 的代码基础上，添加舵机，实现远程开关宿舍灯光功能。

Blinker 连接小爱同学时选择多功能插座，获取多设备相应方式，然后通过训练小爱同学完成语音指令的对应。

# Part 2: 代码实现

```c
/*
  Author: ReModer
  Date: 12/03/2023
  Func: To realize WoL and Auto-Light-Button By Blinker. 
*/

#define BLINKER_WIFI
#define BLINKER_MIOT_LIGHT

#include <WiFi.h>
#include <WiFiUdp.h>
#include <Blinker.h>
#include <Servo.h>

const char *auth = "***"; // here is your blinker device secret key
const char *ssid = "***"; // here is your Wifi id
const char *pswd = "***"; // here is your Wifi password

// Data about WoL
int port = 6022; // every port from 1 to 25565 can be used
byte preamble[] = {0xFF, 0xFF, 0xFF, 0xFF, 0xFF, 0xFF}; 
byte macAddress[] = {0xB0, 0x25, 0xAA, 0x42, 0x8F, 0xCE}; // **-**-**-** is your MAC Address
IPAddress ip(192, 168, 1, 107); // IPAddress is a class with the only constructed function to describe an IP address

// UDP
WiFiUDP UDP;

// Blinker Device
BlinkerButton Button("TurnOnPC"); // The name should be samed as the name on APP
BlinkerButton PreLight("PreLight");
BlinkerButton SufLight("SufLight");

// Servo IDs
const int turnOnServoPin = 4;
const int turnOffServoPin = 5;
Servo turnOnServo, turnOffServo;

void UDPSendPacket(IPAddress ip, int port){
  UDP.beginPacket(ip, port);
  UDP.write(preamble, sizeof(preamble));
  for (int i = 0; i < 16; i++)
    UDP.write(macAddress, sizeof(macAddress));
  UDP.endPacket();
}

void ButtonCallback(const String & state){
  BLINKER_LOG("get button state: ", state);
  UDPSendPacket(ip, port);
}

/* ********** About Servo ********** */
void spin(char ch, int op){
  if (ch == 'p' || ch == 'P'){
    if (op == 0){
      turnOffServo.write(92);
      delay(500);
      turnOffServo.write(81);
      Serial.println("前灯关");
    } else {
      turnOnServo.write(63);
      delay(500);
      turnOnServo.write(81);
      Serial.println("前灯开");
    }
  } else if (ch == 's' || ch == 'S'){
    if (op == 0){
      turnOffServo.write(63);
      delay(500);
      turnOffServo.write(81);
      Serial.println("后灯关");
    } else {
      turnOnServo.write(95);
      delay(500);
      turnOnServo.write(81);
      Serial.println("后灯开");
    }
  }
}

void PreLightCallback(const String & state){
  BLINKER_LOG("get button state: ", state);
  if (state == BLINKER_CMD_ON){
    spin('p', 1);
    PreLight.print("on");
    BlinkerMIOT.powerState("on");
    BlinkerMIOT.print();
  } else if (state == BLINKER_CMD_OFF){
    spin('p', 0);
    PreLight.print("off");
    BlinkerMIOT.powerState("off");
    BlinkerMIOT.print();
  }
}

void SufLightCallback(const String & state){
  BLINKER_LOG("get button state: ", state);
  if (state == BLINKER_CMD_ON){
    spin('s', 1);
    SufLight.print("on");
    BlinkerMIOT.powerState("on");
    BlinkerMIOT.print();
  } else if (state == BLINKER_CMD_OFF){
    spin('s', 0);
    SufLight.print("off");
    BlinkerMIOT.powerState("off");
    BlinkerMIOT.print();
  }
}

void miotMode(uint8_t mode){
  BLINKER_LOG("need set mode: ", mode);

  if (mode == BLINKER_CMD_MIOT_DAY){
    UDPSendPacket(ip, port);
  } else if (mode == BLINKER_CMD_MIOT_NIGHT){
    spin('p', 1);
    PreLight.print("on");
  } else if (mode == BLINKER_CMD_MIOT_COLOR){
    spin('p', 0);
    PreLight.print("off");
  } else if (mode == BLINKER_CMD_MIOT_WARMTH){
    spin('s', 1);
    SufLight.print("on");
  } else if (mode == BLINKER_CMD_MIOT_TV){
    spin('s', 0);
    SufLight.print("off");
  } else if (mode == BLINKER_CMD_MIOT_READING){
    spin('p', 1);
    spin('s', 1);
    PreLight.print("on");
    SufLight.print("on");
  } else if (mode == BLINKER_CMD_MIOT_COMPUTER){
    spin('p', 0);
    spin('s', 0);
    PreLight.print("off");
    SufLight.print("off");
  }
  BlinkerMIOT.mode(mode);
  BlinkerMIOT.print();
}
 
void setup() {
  Serial.begin(115200);
  BLINKER_DEBUG.stream(Serial);

  Blinker.begin(auth, ssid, pswd);

  // assign port
  UDP.begin(port);

  // Servo attach
  turnOnServo.attach(turnOnServoPin, Servo::CHANNEL_NOT_ATTACHED, 45, 120);
  turnOffServo.attach(turnOffServoPin, Servo::CHANNEL_NOT_ATTACHED, 45, 120);

  // CallBack
  Button.attach(ButtonCallback);
  PreLight.attach(PreLightCallback);
  SufLight.attach(SufLightCallback);
  BlinkerMIOT.attachMode(miotMode);
}

void loop() {
  Blinker.run();
}
```

