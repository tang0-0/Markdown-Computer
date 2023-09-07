# RK3568-GPIO控制

## 1. Sysfs接口

### 实现逻辑

芯片的GPIO由芯片的GPIO控制器来管理，GPIO控制器封装在芯片内部，控制器的驱动芯片厂家已经写好了。RK3568有五组GPIO控制器，每组管理32个引脚，对应`/dev`下的`gpiochip0~4`的设备节点。
Linux系统本身就含有GPIO子系统模块，它对下接入GPIO控制器的驱动，对上提供一组API接口给其它驱动调用来管理GPIO控制器。
Sysfs-GPIO通过对GPIO子系统的API的调用实现了一套对用户空间的接口，相当于这是linux官方实现的一个通用GPIO驱动。

### 使用方法
1. 使用时需要打开内核编译选项：Device Drivers-> GPIO Support ->/sys/class/gpio/… (sysfs interface)
2. 导出GPIO引脚，`echo <GPIO编号> > /sys/class/gpio/export`
3. 设置GPIO方向，`echo <out/in> > /sys/class/gpio/gpiox/direction`
4. 读写GPIO值，`echo <1/0> > /sys/class/gpio/gpiox/value`

```shell
// pin = bank*32 + group*8 + X 
// 导出引脚GPIO2_D1, pin = 2*32+3*8+1=89
echo 89 > /sys/class/gpio/export

//配置引脚为输出
echo out/in > /sys/class/gpio/gpio89/direction

//写高电平
echo 1 > /sys/class/gpio/gpio89/value

//读状态
cat /sys/class/gpio/gpio89/value

//取消导出
echo 89 > /sys/class/gpio/unexport

```

## 2. Libgpiod接口

Libgpiod和Sysfs-GPIO的功能是一样的，它是Sysfs-GPIO的改进版。自Linux 4.8起，Sysfs-GPIO接口已被弃用，内核采用libgpiod为用户空间提供接口。之前的版本需要下载安装Libgpiod库才能使用。

参考：<https://blog.csdn.net/DRAXY/article/details/125862761>

## 3. 自己写驱动

按照字符设备驱动框架，调用GPIO子系统提供的接口，来实现某些引脚的驱动。
参考：<>
