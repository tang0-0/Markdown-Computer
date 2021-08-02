# MCU-串口发送实现

>例程仓库：https://gitee.com/ll0_0ll/MCU-UART

## 1.串口发送寄存器为空中断+RingBuff

1. 初始化一段内存空间作循环队列
2. 发送数据时，先将数据放到循环队列中，然后开启发送寄存器为空中断（USART_IT_TXE）
3. 在中断中，判断队列中是否有数据，有数据就发送，没有数据就关闭中断

```c
void uart1_send_bytes(unsigned char *data, unsigned int len)
{

    ringbuf_write(&task_uart1_tx_ringbuf, data, len);

    /* 打开发送寄存器为空中断 */
    USART_ITConfig(USART1, USART_IT_TXE, ENABLE);

}

void task_uart1_tx_handler(void)
{
    unsigned char tmp;
    if (USART_GetITStatus(USART1, USART_IT_TXE) == SET)
    {
        if (ringbuf_get_len(&task_uart1_tx_ringbuf) > 0)
        {
            ringbuf_read(&task_uart1_tx_ringbuf, &tmp, 1);
            USART1->DR = tmp;
        }
        else
        {
            /* 关闭发送寄存器为空中断 */
            USART_ITConfig(USART1, USART_IT_TXE, DISABLE);
        }

    }

}

```

