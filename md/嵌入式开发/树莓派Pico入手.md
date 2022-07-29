# 树莓派Pico入手

## 一、介绍

### 1.1 价格

开发板￥19.00

### 1.2 型号

目前主要有两个型号：

- Pico: 基础版
- Pico-W: 带WIFI芯片
- -H的是指给你焊好排针

## 1.2 性能

1. 核心: 双核 Arm Cortex-M0 + @ 133MHz

2. 存储: 芯片内置264KB SRAM 和2MB的板载闪存，支持最高16MB的片外闪存

3. 功耗: 

4. 外设: 
- 30xGPIO
- 4xADC
- 2xUART
- 2xSPI
- 2xIIC
- 1xUSB1.1

## 二、开发方式

1. MicroPython

2. C/C++

3. Arduino IDE(这个应该是对官网C/C++SDK的封装)

## 三、环境搭建

### 3.1 安装 MicroPython

1. 按住 Pico 开发板上的 BOOTSEL 按钮，然后将 Pico 插入树莓派或 PC 的 USB 接口，然后松开 BOOTSEL 按钮。

2. Pico 会被识别为大容量存储设备。

3. 将下载的 MicroPython UF2 文件放入 RPI-RP2 卷上。你的 Pico 将自动重启，然后 MicroPython 就开始运行了。

4. 

### 3.1 安装IDE-Thonny

官网下载:<https://thonny.org>

### 3.2 交互编程与烧录

## 四、点灯
