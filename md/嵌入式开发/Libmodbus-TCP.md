# Libmodbus-TCP

## 环境配置

VirtualBox+Ubuntu16.04

VirtualBox网络配置成**桥接网卡**，主机和虚拟机之间可以相互ping通。

## 主机例程

官方API文档：<https://libmodbus.org/reference>

### 调用流程

1. 使用`modbus_new_tcp`初始化tcp资源

2. 使用`modbus_set_debug`、`modbus_set_response_timeout`设置参数

3. 使用`modbus_connect`连接从机

4. 使用`modbus_write_bit`、`modbus_read_bits`、`modbus_write_register`、`modbus_read_registers`读/写寄存器

5. 使用`modbus_close`关闭连接，使用`modbus_free`释放资源

### 例程

```c
#include <modbus.h>
#include <stdio.h>
#include <string.h>
#include <unistd.h>
#include <stdint.h>
#include <errno.h>

#define TCP_IP      "10.5.23.52"
#define TCP_PORT    502

void master(void)
{
    modbus_t* ctx = NULL;
    int ret;

    /* 初始化tcp资源 */
    ctx = modbus_new_tcp(TCP_IP,TCP_PORT);
    if(ctx)
    {
        printf("New modbus tcp\n");
    }
    else
    {
        printf("new tcp error:%s\n",modbus_strerror(errno));
    }

    /* 开启调试打印 */
    ret = modbus_set_debug(ctx, TRUE);
    printf("set debug:%d\n",ret);
    /* 设置最大响应超时 */
    ret = modbus_set_response_timeout(ctx, 5, 0);
    printf("set timeout:%d\n",ret);

    /* 连接从机 */
    ret = modbus_connect(ctx);
    if(ret == 0)
    {
        printf("tcp connect success\n");
    }
    else
    {
        printf("tcp connect error:%d|%s\n",ret,modbus_strerror(errno));
        modbus_free(ctx);
        return ;
    }

    uint8_t bit = 0,status = 0;
    uint16_t register_value[64] = {0},value = 0;
    while (1)
    {
        ret = modbus_write_bit(ctx, 1, status);
        printf("write bit:%d|%d\n",status,ret);
        status = ~status;

        ret = modbus_read_bits(ctx, 1, 1, &bit);
        printf("read bit:%d|%d\n",bit,ret);

        ret = modbus_read_input_bits(ctx, 11, 1, &bit);
        printf("read discrete:%d|%d\n",bit,ret);

        ret = modbus_write_register(ctx, 21, value);
        printf("write register:%d|%d\n",value,ret);
        ++value;

        ret = modbus_read_registers(ctx, 21, 1, register_value);
        printf("read hold:%d|%d\n",register_value[0],ret);

        ret = modbus_read_input_registers(ctx, 31, 1, register_value);
        printf("read input:%d|%d\n",register_value[0],ret);

        sleep(3);
    }

    /* 关闭连接 */
    // modbus_close(ctx);
    /* 释放资源 */
    // modbus_free(ctx);
}
```

## 从机例程

### 调用流程

1. 使用`modbus_new_tcp`初始化tcp资源
2. 使用`modbus_set_debug`设置参数
3. 使用`modbus_mapping_new_start_address`初始化寄存器资源，并映射地址
4. 设置寄存器初始值
5. 使用`modbus_tcp_listen`开启监听模式
6. 使用`modbus_tcp_accept`阻塞等待从机连接
7. 使用`modbus_receive`接收主机请求，使用`modbus_reply`返回响应
8. 使用`modbus_close`关闭连接，使用`modbus_mapping_free`释放寄存器资源，使用`modbus_free`释放tcp资源

### 例程

```c
void slave()
{
    modbus_t* ctx = NULL;
    int ret;

    /* 初始化tcp资源 */
    ctx = modbus_new_tcp(NULL,TCP_PORT);
    if(ctx)
    {
        printf("New modbus tcp\n");
    }
    else
    {
        printf("new tcp error:%s\n",modbus_strerror(errno));
    }

    /* 开启调试打印 */
    ret = modbus_set_debug(ctx, TRUE);
    printf("set debug:%d\n",ret);

    /* 设置从机寄存器映射，寄存器起始地址和个数 */
    modbus_mapping_t *mb_mapping = NULL;
    mb_mapping = modbus_mapping_new_start_address(1,MODBUS_MAX_READ_BITS,
                                                  11,MODBUS_MAX_READ_BITS,
                                                  21,MODBUS_MAX_READ_REGISTERS,
                                                  31,MODBUS_MAX_READ_REGISTERS);
    if(!mb_mapping)
    {
        printf("new map error:%s\n",modbus_strerror(errno));
        modbus_free(ctx);
        return ;
    }

    /* 设置寄存器的值 */
    mb_mapping->tab_bits[0] = 0xf0;
    mb_mapping->tab_input_bits[0] = 0x0f;
    mb_mapping->tab_registers[0] = 123;
    mb_mapping->tab_registers[1] = 456;
    mb_mapping->tab_input_registers[0] = 789;
    mb_mapping->tab_input_registers[1] = 666;

    int tcp_fd;
    tcp_fd = modbus_tcp_listen(ctx, 1);
    if(tcp_fd < 0)
    {
        printf("tcp listen error:%s\n",modbus_strerror(errno));
        modbus_mapping_free(mb_mapping);
        modbus_free(ctx);
        return ;
    }
    else
    {
        printf("tcp listen success\n");
    }
    modbus_tcp_accept(ctx, &tcp_fd);

    uint8_t rsp[MODBUS_TCP_MAX_ADU_LENGTH];
    while (1)
    {
        ret = modbus_receive(ctx,rsp);
        if(ret >= 0)
        {
            printf("rev:%d|",ret);
            // for(int i=0;i<ret;i++)
            //     printf("%02x ",rsp[i]);
            // printf("\n");
            modbus_reply(ctx,rsp,ret,mb_mapping);
        }
        else
        {
            printf("receive error:%s\n",modbus_strerror(errno));
        }
    }

    // modbus_close(ctx);
    // modbus_mapping_free(mb_mapping);
    // modbus_free(ctx);
}
```
