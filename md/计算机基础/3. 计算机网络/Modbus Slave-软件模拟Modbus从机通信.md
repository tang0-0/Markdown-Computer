# Modbus Slave-软件模拟Modbus从机通信

## 1.介绍
Modbus Slave可以仿真32个从设备/地址域。主要用来模拟Modbus从站设备,接收主站的命令包,返回数据包。帮可以32个窗口中模拟多达32个Modbus子设备。支持功能01, 02, 03, 04, 05, 06, 15, 16, 22和23，监视串口数据。

## 2.安装
下载地址：<>

## 3.使用
1. Setup->Slave Definiton
    <img src="C:\Users\Dell\Desktop\1.png" alt="image-20201014230710834" style="zoom:60%;" />
    
    Function表示功能码
    Address表示设备地址
    Quantity表示寄存器个数
    Display表示数据存放格式
    
3. 设定寄存器值
    <img src="C:\Users\Dell\Desktop\2.png" alt="image-20201014231743975" style="zoom:60%;" />
双击数据填写框即可设置数据，勾选Auto increment可以是数值每秒加1
    
4. Connection->Connect
    <img src="C:\Users\Dell\Desktop\3.png" alt="image-20201014232452770" style="zoom:60%;" />
可能出现选项是灰的情况，这是因为长时间没连接，重新打开软件就可以了。
点Connection需要注册码，但是不注册也可以用30天。
   
5. 查看Display->Commication...
    <img src="C:\Users\Dell\Desktop\4.jpg" alt="370256-20180520161815233-1154534505" style="zoom:60%;" />