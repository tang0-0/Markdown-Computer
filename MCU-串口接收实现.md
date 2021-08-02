# MCU-串口接收实现

>例程仓库：https://gitee.com/ll0_0ll/MCU-UART

## 1.串口接收中断+空闲中断

>空闲中断是接受数据后出现一个字节的高电平(空闲)状态,就会触发空闲中断。
>接收中断是接收一个字节后就会触发。

1. 串口开启接收中断和空闲中断（RXNE+IDLE）
2. 在接收中断中将数据放入接收buff
3. 在空闲中断中发出信号，通知上层应用数据帧接收完毕

```c
void USART1_IRQHandler(void)
{
    u8 tmp;
    if (USART_GetITStatus(USART1, USART_IT_RXNE) == SET)//接收中断
    {
        tmp = USART_ReceiveData(USART1);
        ringbuf_write(tmp, &g_ringbuf);
        USART_ClearITPendingBit(USART1, USART_IT_RXNE);
    }
    if(USART_GetITStatus(USART1, USART_IT_IDLE) == SET) //空闲中断
    {
        has_one_frame = 1;

        USART1->SR;
        USART1->DR;
    }

}
```


## 2.串口空闲中断+循环DMA+RingBuff

>这种方法的缺点是一旦数据处理比较慢，而剩余缓存空间不够大，那么未处理的数据将自动被DMA接收的新数据所覆盖，所以缓存大小一定要有富余

1. 开启空闲中断和相应串口DMA循环模式
2. 初始化一段内存空间作循环队列
2. 串口来了数据，DMA会自动存放到队列的那段内存上，并且开了循环模式它会循环存放
3. 在空闲中断里，查询出数据帧的长度，移动队列尾，发出信号


```c
void USART1_IRQHandler(void)
{
    uint16_t count;
    static uint16_t last_count = UART1_RINGBUF_SIZE;
    if (USART_GetITStatus(USART1, USART_IT_IDLE) == SET)
    {

        count = DMA_GetCurrDataCounter(USART_TX_DMA_CHANNEL); //获取剩余的数据数量
        if ((last_count - count) <= 0)
        {
            last_count += UART1_RINGBUF_SIZE;
        }
        ringbuf_move_tail(&task_uart1_ringbuf, last_count - count);
        LOG_A("count:%d,last_count:%d", count, last_count);

        last_count = count;
        has_one_frame = 1;

        USART1->SR;
        USART1->DR;
    }

}
```