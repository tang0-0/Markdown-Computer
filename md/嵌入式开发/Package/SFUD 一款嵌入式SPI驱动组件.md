# SFUD 一款嵌入式SPI FLASH驱动组件

>源仓库：https://github.com/armink/SFUD
>移植例程仓库：https://gitee.com/ll0_0ll/Packages-practice

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

### 1.准备

芯片：STM32F103xE		W25Q128
外设：SPI2	UART1
软件：串口调试工具

### 2.适配接口

需要适配的接口都在`sfud_port.c`中
```c
static sfud_err spi_write_read(const sfud_spi *spi, const uint8_t *write_buf, size_t write_size, uint8_t *read_buf, size_t read_size);

static sfud_err qspi_read(const struct __sfud_spi *spi, uint32_t addr, sfud_qspi_read_cmd_format *qspi_read_cmd_format, uint8_t *read_buf, size_t read_size);

sfud_err sfud_spi_port_init(sfud_flash *flash);

void sfud_log_debug(const char *file, const long line, const char *format, ...);

void sfud_log_info(const char *format, ...);

```

### 3.适配步骤

1. 定义flash对象(`sfud_cfg.h`)

```c
enum {
    SFUD_XXXX_DEVICE_INDEX = 0,
};

#define SFUD_FLASH_DEVICE_TABLE                                                \
{                                                                              \
    [SFUD_XXXX_DEVICE_INDEX] = {.name = "TEST", .spi.name = "SPI2"},           \
}
```

2. 适配接口(`sfud_port.c`)

```c
/* 1.初始化接口 */
sfud_err sfud_spi_port_init(sfud_flash *flash)
{
    sfud_err result = SFUD_SUCCESS;

    switch (flash->index)
    {
    case SFUD_XXXX_DEVICE_INDEX:
    {
        spi2_init();
        /* 同步 Flash 移植所需的接口及数据 */
        flash->spi.wr = spi_write_read;
        flash->spi.lock = spi_lock;
        flash->spi.unlock = spi_unlock;
        /* about 100 microsecond delay */
        flash->retry.delay = retry_delay_100us;
        /* adout 3 seconds timeout */
        flash->retry.times = 3 * 10000;

        break;
    }
    }
    return result;
}

/* 2.读写接口 */
static sfud_err spi_write_read(const sfud_spi *spi, const uint8_t *write_buf,
                               size_t write_size, uint8_t *read_buf,
                               size_t read_size)
{
    sfud_err result = SFUD_SUCCESS;
    uint8_t send_data, read_data;

    if (write_size)
    {
        SFUD_ASSERT(write_buf);
    }
    if (read_size)
    {
        SFUD_ASSERT(read_buf);
    }

    SPI_FLASH_CS_EN();
    for (size_t i = 0, retry_times; i < write_size + read_size; i++)
    {
        /* 先写缓冲区中的数据到 SPI 总线，数据写完后，再写 dummy(0xFF) 到 SPI 总线 */
        if (i < write_size)
        {
            send_data = *write_buf++;
        }
        else
        {
            send_data = SFUD_DUMMY_DATA;
        }
        /* 发送数据 */
        retry_times = 1000;
        while (SPI_I2S_GetFlagStatus(SPI2, SPI_I2S_FLAG_TXE) == RESET)
        {
            SFUD_RETRY_PROCESS(NULL, retry_times, result);
        }
        if (result != SFUD_SUCCESS)
        {
            goto exit;
        }
        SPI_I2S_SendData(SPI2, send_data);
        /* 接收数据 */
        retry_times = 1000;
        while (SPI_I2S_GetFlagStatus(SPI2, SPI_I2S_FLAG_RXNE) == RESET)
        {
            SFUD_RETRY_PROCESS(NULL, retry_times, result);
        }
        if (result != SFUD_SUCCESS)
        {
            goto exit;
        }
        read_data = SPI_I2S_ReceiveData(SPI2);
        /* 写缓冲区中的数据发完后，再读取 SPI 总线中的数据到读缓冲区 */
        if (i >= write_size)
        {
            *read_buf++ = read_data;
        }
    }

exit:
    SPI_FLASH_CS_DIS();

    return result;
}

```

3. 调用接口(可在`sfud.h`中查看可供调用的API)

```C
int main(void)
{
    sfud_flash *flash;
    sfud_err result = SFUD_ERR_NOT_FOUND;
    uint8_t sfud_Write_buf[] = "SFUD TEST!";
    uint8_t sfud_Read_buf[30];
    uint32_t addr = 0x00000000;
    size_t size = sizeof(sfud_Write_buf);

    /* 串口一用来打印调试信息 */
    single_uart_init();
    printf("enter sfud test\r\n");

    flash = sfud_get_device(SFUD_XXXX_DEVICE_INDEX);

    if (sfud_device_init(flash) == SFUD_SUCCESS)
        printf("SFUD Init Success\r\n");
    else
        printf("SFUD Init Failed\r\n");
    
    result = sfud_erase(flash, addr, size);
    if (result == SFUD_SUCCESS)
    {
        printf("Erase the %s flash data finish. Start from 0x%08X, size is %zu.\r\n", flash->name,
               addr, size);
    }
    else
    {
        printf("Erase the %s flash data failed.\r\n", flash->name);
    }

    result = sfud_write(flash, addr, size, sfud_Write_buf);
    if (result == SFUD_SUCCESS)
    {
        printf("Write the %s flash data finish. Start from 0x%08X, size is %zu.\r\n", flash->name,
               addr, size);
    }
    else
    {
        printf("Write the %s flash data failed.\r\n", flash->name);

    }

    result = sfud_read(flash, addr, size, sfud_Read_buf);
    if (result == SFUD_SUCCESS)
    {
        printf("Read the %s flash data success. Start from 0x%08X, size is %zu.\r\n", flash->name,
               addr, size);
        printf("The data is:%s", sfud_Read_buf);
        printf("\r\n");
    }
    else
    {
        printf("Read the %s flash data failed.\r\n", flash->name);
    }
    

    while(1);
}

```

4. 打印信息

![](SFUD%20%E4%B8%80%E6%AC%BE%E5%B5%8C%E5%85%A5%E5%BC%8FSPI%E9%A9%B1%E5%8A%A8%E7%BB%84%E4%BB%B6.assets/sfud.png)
