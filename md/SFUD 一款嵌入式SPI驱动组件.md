# SFUD 一款嵌入式SPI FLASH驱动组件

## 简介

### 1.功能

通常要驱动SPI Flash要根据厂家的手册写好各种接口，而这个组件通过SFDP标准，只要适配几个接口它就可以自己去获取那些厂家的操作命令，然后调用它的接口就可以驱动flash了。这样一来驱动写起来会简单点，而来换flash时改动也会少点。

### 2.SFDP

**什么是 SFDP** ：它是 JEDEC （固态技术协会）制定的串行 Flash 功能的参数表标准，最新版 V1.6B （[点击这里查看](https://www.jedec.org/standards-documents/docs/jesd216b)）。该标准规定了，每个 Flash 中会存在一个参数表，该表中会存放 Flash 容量、写粒度、擦除命令、地址模式等 Flash 规格参数。目前，除了部分厂家旧款 Flash 型号会不支持该标准，其他绝大多数新出厂的 Flash 均已支持 SFDP 标准。所以该库在初始化时会优先读取 SFDP 表参数。

> 具体可参考这篇文章：[SFDP（Serial Flash Discoverable Parameters）_高桥凉介。的博客-CSDN博客](https://blog.csdn.net/qq_45472315/article/details/114489335)

**不支持 SFDP 怎么办** ：如果该 Flash 不支持 SFDP 标准，SFUD 会查询配置文件 ( [`/sfud/inc/sfud_flash_def.h`](https://github.com/armink/SFUD/blob/4bee2d0417a7ce853cc7aa3639b03fe825611fd9/sfud/inc/sfud_flash_def.h#L116-L142) ) 中提供的 **Flash 参数信息表** 中是否支持该款 Flash。如果不支持，则可以在配置文件中添加该款 Flash 的参数信息（添加方法详细见 [2.5 添加库目前不支持的 Flash](#25-添加库目前不支持的-flash)）。获取到了 Flash 的规格参数后，就可以实现对 Flash 的全部操作。

### 3.主要特点

主要特点：支持 SPI/QSPI 接口、面向对象（同时支持多个 Flash 对象）、可灵活裁剪、扩展性强、支持 4 字节地址
资源占用：

- 标准占用：RAM:0.2KB ROM:5.5KB
- 最小占用：RAM:0.1KB ROM:3.6KB

## 移植

### 准备

芯片：STM32F103
外设：

### 适配接口

需要适配的接口都在`sfud_port.c`中
```c
static sfud_err spi_write_read(const sfud_spi *spi, const uint8_t *write_buf, size_t write_size, uint8_t *read_buf, size_t read_size);

static sfud_err qspi_read(const struct __sfud_spi *spi, uint32_t addr, sfud_qspi_read_cmd_format *qspi_read_cmd_format, uint8_t *read_buf, size_t read_size);

sfud_err sfud_spi_port_init(sfud_flash *flash);

void sfud_log_debug(const char *file, const long line, const char *format, ...);

void sfud_log_info(const char *format, ...);

```

### 适配步骤

1. 适配接口
2. 定义flash对象
3. 调用接口

