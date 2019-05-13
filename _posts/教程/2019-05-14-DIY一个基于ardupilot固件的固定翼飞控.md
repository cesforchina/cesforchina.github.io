---
layout: blog
istop: force
title: "DIY一个基于ardupilot固件的固定翼飞控"
background-image: https://cesforchina.files.wordpress.com/2019/05/snipaste_2019-02-13_20-34-23.jpg
date: 2019-05-14 00:48:56
category: 教程
tags:
- 飞控
- ArduPliot
- FPV
- 固定翼
---
# 注意事项
## 本飞控基于开源飞控F4BY二次开发而来，对接口和传感器进行了修改和升级，命名为‘K’，意为“King”
## 本人未对此飞控进行充足的测试，由于使用本飞控而造成飞机失控、坠毁、丢失等各种意外的，本人概不负责！

![Screenshot](https://cesforchina.files.wordpress.com/2019/05/snipaste_2019-02-13_20-34-57.jpg)

# 硬件规格
## 处理器
### 带FPU STM32 F407的单32位ARM Cortex M4内核
## 传感器
### InvenSense MPU6000 IMU（加速器，陀螺仪)
### MS5611气压计
### IST8310指南针
## 功率
### 2个独立的3,3v LDO，用于CPU、传感器、CAN，SD卡和Fram
### 反向电压和过压电源保护
### 电路板电压和伺服电压传感器
## 接口
### 5个UART串行端口，1个带逆变器，用于frsky telemertry
### 高达12x PWM输出
### Spektrum DSM / DSM2 / DSM-X卫星输入
### Futaba S.BUS输入支持（带外置逆变器）
### PPM和信号
### RSSI（PWM或电压）输入
### I2C，SPI，CAN，USB
### 3.3V和6.6V ADC输入
## 外形尺寸
### 50*30.5mm
## 其他
### micro SD卡（用于日志）
### Fram储存参数

# 软件




# Tags
![Screenshot](https://cesforchina.files.wordpress.com/2019/05/snipaste_2019-02-13_20-32-28.jpg)
![Screenshot](https://cesforchina.files.wordpress.com/2019/05/snipaste_2019-02-13_20-33-28.jpg)




## How to start

1. Grab a transponder (build one yourself or get one from an official reseller (currently working on this..)). If you build one yourself here's [how to upload the software](docs/Transponder%20Update.md).
2. Plug it in a free 5V source. Make sure you can draw enough current. Short pulses are over 100mA. Maybe an extra 47-100µF Elko on 5V rails is recommended if you notice some trouble. To set up your transponder ID follow [this guide](docs/Transponder.md).
3. To set up the receiver follow [this guide](docs/Receiver.md).

Now you're ready to go :)

_ToDo :_  
_- Wireless Usage_  
_- What you need_


## What's next

I'm working on a big update, but it's currently a secret :P
Just kidding. I have to check if what I'm planning is actually possible. If it is I will post some info here soon ;)

## Weblinks

Main discussion about OpenLap occurs in the following Facebook group :  
https://www.facebook.com/groups/1047398441948165/  
Most of the discussions are in German, but I will try to make an english version of my posts in the future.

There's also an official website, but because of limited time the info there is out of date...  
www.openlap.de

## Thanks for your interest in OpenLap
Always feel free to contact me, I will try to answer asap.
Any "professional" help is welcome!
