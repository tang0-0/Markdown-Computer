# Libmodbus-RTU使用

## 介绍

Libmodbus是一款用C编写的modbus库，支持**modbus-RTU**的master&slave和**modbus-TCP**的master&slave。

官方API文档：<https://libmodbus.org/reference>

## 测试环境

VirtualBox+Ubuntu16.04
Mthings-modbus主从机模拟软件

## Master调用流程

1. 使用`modbus_new_rtu`初始化RTU资源
2. 使用`modbus_rtu_set_serial_mode`、`modbus_set_slave`、`modbus_set_response_timeout`设置参数
3. 使用`modbus_connect`连接从机
4. 使用`modbus_read_bits`、`modbus_write_bit`、`modbus_read_registers`、`modbus_write_register`读/写寄存器
5. 使用`modbus_close`关闭连接
6. 使用`modbus_free`释放资源

```c
#include <modbus.h>
#include <stdio.h>
#include <string.h>
#include <unistd.h>
#include <stdint.h>
#include <errno.h>

#define UART_PORT   "/dev/ttyS0"
#define UART_BAUD   115200

void master_demo()
{
    modbus_t *ctx = NULL;
    int ret = 0;

    /* 初始化资源 */
    ctx = modbus_new_rtu(UART_PORT,UART_BAUD,'N',8,1);
    if(!ctx)
    {
        printf("new rtu:%s\n",modbus_strerror(errno));
        return;
    }

    /* 打开调试打印 */
    ret = modbus_set_debug(ctx, TRUE);
    printf("set debug:%d\n",ret);
    /* 设置串口模式 */
    ret = modbus_rtu_set_serial_mode(ctx, MODBUS_RTU_RS232);
    printf("set mode:%d\n",ret);
    /* 设置slaveID */
    ret = modbus_set_slave(ctx, 0x01);
    printf("set slave:%d\n",ret);
    /* 设置最大响应超时时间 */
    ret = modbus_set_response_timeout(ctx, 5, 0);
    printf("set timeout:%d\n",ret);
    /* 连接从机 */
    ret = modbus_connect(ctx);
    printf("connect:%d\n",ret);
    if(ret) 
    {
        printf("connect error:%s\n",modbus_strerror(errno));
        modbus_free(ctx);
        return;
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

## Slave调用流程

1. 使用`modbus_new_rtu`初始化RTU资源
2. 使用`modbus_rtu_set_serial_mode`、`modbus_set_slave`设置参数
3. 使用`modbus_mapping_new_start_address`初始化寄存器资源，映射寄存器起始地址和个数
4. 设置寄存器的初始值
5. 使用`modbus_connect`连接主机
6. 使用`modbus_receive`接收请求，使用`modbus_reply`检查请求并响应
7. 使用`modbus_close`关闭连接
8. 使用`modbus_mapping_free`释放寄存器资源，使用`modbus_free`释放从机资源

```c
void slave_demo()
{
    modbus_t *ctx = NULL;
    int ret = 0;

    ctx = modbus_new_rtu(UART_PORT,UART_BAUD,'N',8,1);
    if(!ctx)
    {
        printf("new rtu:%s\n",modbus_strerror(errno));
        return;
    }

    ret = modbus_set_debug(ctx, TRUE);
    printf("set debug:%d\n",ret);

    ret = modbus_rtu_set_serial_mode(ctx, MODBUS_RTU_RS232);
    printf("set mode:%d\n",ret);

    ret = modbus_set_slave(ctx, 0x01);
    printf("set slave:%d\n",ret);

    /* 设置从机寄存器映射，寄存器起始地址和个数 */
    modbus_mapping_t *mb_mapping = NULL;
    mb_mapping = modbus_mapping_new_start_address(1,MODBUS_MAX_READ_BITS,
                                                  11,MODBUS_MAX_READ_BITS,
                                                  21,MODBUS_MAX_READ_REGISTERS,
                                                  31,MODBUS_MAX_READ_REGISTERS);
    if(!mb_mapping)
    {
        printf("new map error:%s",modbus_strerror(errno));
        return ;
    }

    /* 设置寄存器的值 */
    mb_mapping->tab_bits[0] = 0xf0;
    mb_mapping->tab_input_bits[0] = 0x0f;
    mb_mapping->tab_registers[0] = 123;
    mb_mapping->tab_registers[1] = 456;
    mb_mapping->tab_input_registers[0] = 789;
    mb_mapping->tab_input_registers[1] = 666;

    ret = modbus_connect(ctx);
    printf("connect:%d\n",ret);
    if(ret) 
    {
        printf("connect error:%s\n",modbus_strerror(errno));
        modbus_free(ctx);
        return;
    }

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
