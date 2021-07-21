# SWD调试接口

<img src="https://img-blog.csdnimg.cn/20190107193444580.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3UwMTIyNDc0MTg=,size_16,color_FFFFFF,t_70" alt="img" style="zoom: 60%;" /><img src="C:\Users\Dell\Pictures\Saved Pictures\O1CN013cxMBF1aChAQU9xaD_!!738263294.jpg" alt="img" style="zoom:43%;" />

随着ARM公司对Cortex系列的推出，采样SWD方式调试成了大家的首选。SWD不仅速度可以与JTAG媲美，而且使用的调试线少得多。很多人在采样SWD方式进行调试时，一般都是采用4线：

| VCC       | 电源                                                         |
| --------- | ------------------------------------------------------------ |
| **GND**   | **公共地**                                                   |
| **SWDIO** | **串行数据输入输出，作为仿真信号的双向数据信号线，建议上拉** |
| **SWCLK** | **串行时钟输入，作为仿真信号的时钟信号线，建议下拉**         |

- 若不小心禁用了SWD接口，可以通过BOOT0拉高后擦除
- 链接: https://pan.baidu.com/s/1UB3J1wSUxQCifPfkfFtRIg 提取码: 6ehk

