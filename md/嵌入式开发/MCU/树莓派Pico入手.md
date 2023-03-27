# 树莓派Pico入手

> [树莓派 Pico 中文站](https://pico.org.cn/)

---

## 一、介绍

### 1.1 价格

开发板￥19.00

### 1.2 型号

目前主要有两个型号：

- Pico: 基础版
- Pico-W: 板载WIFI芯片
- -H的是指给你焊好排针

### 1.3 性能

![屏幕截图 2022-07-29 194854.jpg](C:\Users\Dell\Desktop\屏幕截图%202022-07-29%20194854.jpg)

1. 核心: 双核 Arm Cortex-M0 + @ 133MHz

2. 存储: 芯片内置264KB SRAM 和2MB的板载闪存，支持最高16MB的片外闪存

3. 电源: 芯片是3.3V供电，开发板支持2~5V输入

4. 外设: 
- 26xGPIO
- 3xADC
- 16xPWM
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

4. 使用串口终端打开虚拟串口，波特率随便选一个，按下回车有提示符就说明安装成功了
   
   ![屏幕截图 2022-07-29 194637.jpg](C:\Users\Dell\Desktop\屏幕截图%202022-07-29%20194637.jpg)

### 3.2 安装IDE-Thonny

官网下载:<https://thonny.org>

安装后打开，`initial settings`选择`Standard`

在`工具->设置`中将解释器选择为`MicroPython(Raspberry Pi Pico)`，端口设置为连接的串口

![屏幕截图 2022-07-29 201800.jpg](C:\Users\Dell\Desktop\屏幕截图%202022-07-29%20201800.jpg)

### 3.3 交互编程与脚本编程

1. 交互编程
   
   交互编程就是通过命令行将程序写给MicroPython，这种方式写起来比较麻烦，而且上电后程序就不在了需要重新输入。
   
   ![屏幕截图 2022-07-29 202648.jpg](C:\Users\Dell\Desktop\屏幕截图%202022-07-29%20202648.jpg)

2. 脚本编程
   
   程序写好后，命名为`main.py`，保存到Pico中，这样重新上电后程序会自动执行。
   
   ![屏幕截图 2022-07-29 204110.jpg](C:\Users\Dell\Desktop\屏幕截图%202022-07-29%20204110.jpg)

## 四、点灯

```c
import machine
import utime

led = machine.Pin(25, machine.Pin.OUT)

while True:
    led.value(1)
    utime.sleep(1)
    led.value(0)
    utime.sleep(1)
```
