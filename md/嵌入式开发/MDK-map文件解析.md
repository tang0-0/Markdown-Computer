# MDK-map文件解析


## 分类

1. Section Cross References：模块、函数交叉引用；
2. Removing Unused input sections from the image：移除未使用的模块；
3. Image Symbol Table：映射符号表；
4. Memory Map of the image：内存分布；
5. Image component sizes：存储组成大小

## 1.Section Cross References

指的是各个源文件生成的模块、函数之间相互引用的关系。

## 2.Removing Unused input sections from the image

指的是工程中未用到的模块

## 3.Image Symbol Table

函数和变量所在的地址

## 4.Memory Map of the image

指示内存分布
Image Entry point : 程序入口
Load Region LR_IROM1 ：ROM中的内存分布
Execution Region RW_IRAM1：RAM中的内存分布

## 5.Image component sizes

存储组成大小，其实主要就是对模块进行汇总存储大小信息

Code：指代码的大小；
RO-data：指除了内联数据(inline data)之外的常量数据；
RW-data：指可读写（RW）、已初始化的变量数据；
ZI-data：指未初始化（ZI）的变量数据

提醒：RW-data已初始化的数据会存储在Flash中，上电会从FLASH搬移至RAM中。
ROM Size = Code + RO Data + RW Data
RAM Size = RW-data + ZI-data

