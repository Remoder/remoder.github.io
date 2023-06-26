---
title: 'IoT Learning: Turn On PC by WoL'
date: 2023-03-10 18:17:58
tags: [IoT, Wake on Lan, ESP32, Arduino, blinker]
description: 借助 ESP32 平台，接入小爱同学语音控制，以通过 WoL 对电脑进行远程唤醒。
abbrlink: Turn-on-PC-by-WoL
cover: /img/Turn-on-PC-by-WoL.jpg
categories:
---

# Part 1: 思路介绍

WoL (Wake On Lan)，即从局域网唤醒电脑，亦或配合路由器远程唤醒。具体过程在于，透过局域网设备对电脑网卡发送命令，使其从休眠或关机状态唤醒转换成开机状态。  

通过 ESP32 设备向电脑端口发送命令通信，激活 WoL 便可实现开机。同时，为便利生活，考虑借助 [点灯科技](https://diandeng.tech/) 将 ESP32 设备接入小爱同学以进行语音控制。

# Part 2: 准备工作

1. [搭建 Aruidno + ESP32 开发环境]()
2. [开启电脑的 WoL 功能](https://post.smzdm.com/p/a7d70m4g/)
3. [安装 blinker 库](https://diandeng.tech/doc/getting-start-esp32-wifi)

# Part 3: 编写代码

## 通过 UDP 进行通信实现 WoL 功能

假设被唤醒的 PC 网卡 MAC 地址为：`01 02 03 04 05 06`，则 WoL 魔法包的结构即为：`FF FF FF FF FF FF | 01 02 03 04 05 06 ...(Repeat for 16 times totally)... 01 02 03 04 05 06`。  

其中头部 `FF FF FF FF FF FF` 固定不变，后部为 MAC 地址的十六次重复。有时数据包还会紧接着4-6字节的密码信息，本文中设为空。
  
将此数据包由任意空闲端口发送至目标计算机即可唤醒。

代码如下：

```c
/*
  Author: ReModer
  Date: 10/03/2023
  Func: To realize WoL By Blinker. 
*/

#include <WiFi.h>
#include <WiFiUdp.h>

const char *ssid = "***"; // here is your Wifi id
const char *pswd = "***"; // here is your Wifi password

// Data about WoL
int port = **; // every port from 1 to 25565 can be used
byte preamble[] = {0xFF, 0xFF, 0xFF, 0xFF, 0xFF, 0xFF}; 
byte macAddress[] = {0x**, 0x**, 0x**, 0x**, 0x**, 0x**}; // **-**-**-** is your MAC Address
IPAddress ip(**, **, **, **); // IPAddress is a class with the only constructed function to describe an IP address

// UDP
WiFiUDP UDP;

void UDPSendPacket(IPAddress ip, int port){
  UDP.beginPacket(ip, port);
  UDP.write(preamble, sizeof(preamble));
  for (int i = 0; i < 16; i++)
    UDP.write(macAddress, sizeof(macAddress));
  UDP.endPacket();
}

void setup() {
  Serial.begin(115200);

  // init WIFI driver
  WiFi.mode(WIFI_STA);
  WiFi.begin(ssid, pswd);
  
  // assign port
  UDP.begin(port);

  Serial.println("");
  Serial.println("... Start Successfully ...");
}

void loop() {
  UDPSendPacket(ip, port);
  Serial.println("Send succesfully once again.");
  delay(500);
}

```

当然，我们不需要一次次重启电脑来验证程序，可以利用 [Wake on Lan Monitor](https://www.depicus.com/wake-on-lan/wake-on-lan-monitor) 验证数据包是否传输成功，保证端口号一致即可。


## 连接 blinker 实现手机端 APP 操控 
添加 blinker 相关部分即可，基本没难度，可以去参考 [官方文档](https://diandeng.tech/doc)。

如果不追求语音控制的话，到这一步其实已经可以结束了。

```c
/*
  Author: ReModer
  Date: 10/03/2023
  Func: To realize WoL By Blinker. 
*/

#define BLINKER_WIFI

#include <WiFi.h>
#include <WiFiUdp.h>
#include <Blinker.h>

const char *auth = "***"; // here is your blinker device secret key
const char *ssid = "***"; // here is your Wifi id
const char *pswd = "***"; // here is your Wifi password

// Data about WoL
int port = **; // every port from 1 to 25565 can be used
byte preamble[] = {0xFF, 0xFF, 0xFF, 0xFF, 0xFF, 0xFF}; 
byte macAddress[] = {0x**, 0x**, 0x**, 0x**, 0x**, 0x**}; // **-**-**-** is your MAC Address
IPAddress ip(**, **, **, **); // IPAddress is a class with the only constructed function to describe an IP address

// UDP
WiFiUDP UDP;

// Blinker Device
BlinkerButton Button("TurnOnPC"); // The name should be samed as the name on APP

void UDPSendPacket(IPAddress ip, int port){
  UDP.beginPacket(ip, port);
  UDP.write(preamble, sizeof(preamble));
  for (int i = 0; i < 16; i++)
    UDP.write(macAddress, sizeof(macAddress));
  UDP.endPacket();

  Serial.println("Send succesfully once again.");
}

void ButtonCallback(const String & state){
  BLINKER_LOG("get button state: ", state);
  UDPSendPacket(ip, port);
}

void setup() {
  Serial.begin(115200);
  BLINKER_DEBUG.stream(Serial);

  Blinker.begin(auth, ssid, pswd);
  Button.attach(ButtonCallback);

  UDP.begin(port);

  Serial.println("");
  Serial.println("... Start Successfully ...");
}

void loop() {
  Blinker.run();
}

```

## 接入小爱同学实现语音控制
按照 [Blinker 设备接入小爱同学](https://diandeng.tech/doc/xiaoai) 所讲，加入插座类部分即可，注意回调函数与及时反馈。

```c
/*
  Author: ReModer
  Date: 10/03/2023
  Func: To realize WoL By Blinker. 
*/

#define BLINKER_WIFI
#define BLINKER_MIOT_OUTLET

#include <WiFi.h>
#include <WiFiUdp.h>
#include <Blinker.h>

const char *auth = "3d1d35761f0a"; // here is your blinker device secret key
const char *ssid = "是你蹭不到的"; // here is your Wifi id
const char *pswd = "sncbdd330"; // here is your Wifi password

// Data about WoL
int port = 6022; // every port from 1 to 25565 can be used
byte preamble[] = {0xFF, 0xFF, 0xFF, 0xFF, 0xFF, 0xFF}; 
byte macAddress[] = {0xB0, 0x25, 0xAA, 0x42, 0x8F, 0xCE}; // **-**-**-** is your MAC Address
IPAddress ip(192, 168, 1, 107); // IPAddress is a class with the only constructed function to describe an IP address

// UDP
WiFiUDP UDP;

// Blinker Device
BlinkerButton Button("TurnOnPC"); // The name should be samed as the name on APP

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

void miotPowerState(const String & state){
  BLINKER_LOG("need set power state: ", state);
  if (state == BLINKER_CMD_ON){
    UDPSendPacket(ip, port);
    BlinkerMIOT.powerState("on");
    BlinkerMIOT.print();
  } else if (state == BLINKER_CMD_OFF){
    BlinkerMIOT.powerState("off");
    BlinkerMIOT.print();
  }
}

void setup() {
  Serial.begin(115200);
  BLINKER_DEBUG.stream(Serial);


  Blinker.begin(auth, ssid, pswd);
  Button.attach(ButtonCallback);

  // assign port
  UDP.begin(port);

  // CallBack
  BlinkerMIOT.attachPowerState(miotPowerState);
}

void loop() {
  Blinker.run();
}

```


# Part 4: 参考资料
[玩转WakeOnLan(远程开机)](https://www.jianshu.com/p/22cbb5e9036a)  

[Python WOL/WakeOnLan/网络唤醒数据包发送工具](https://blog.csdn.net/weixin_33739523/article/details/92981370)

[网络唤醒(WOL)全解指南：原理篇](https://cloud.tencent.com/developer/article/1352026#)

[使用Arduino开发ESP32（04）：UDP通讯使用说明](https://blog.csdn.net/Naisu_kun/article/details/86300456)

[小爱同学+ESP8266-01S控制家里的灯（Blinker接入）](https://zhuanlan.zhihu.com/p/87212242)

[小爱同学+ESP8266+手机APP远程开启电脑](https://zhuanlan.zhihu.com/p/93143539)

[小爱同学使用 esp32 网络唤醒电脑、关闭电脑](https://github.com/tty228/udp_turn_off)