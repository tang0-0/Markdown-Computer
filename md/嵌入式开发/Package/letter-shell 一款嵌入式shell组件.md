# letter-shell 一款嵌入式shell组件

源仓库:<https://github.com/NevermindZZT/letter-shell>
移植例程:<https://gitee.com/ll0_0ll/Packages-practice>

## 简介

### 1.效果
这款shell组件实现的效果，是让单片机的串口可以像电脑的CMD命令行一样进行交互。输入规定的命令和参数，就会调用相应的接口，然后返回结果。

![](C:/Users/ta/Pictures/Camera%20Roll/shell1.png)

### 2.功能

- 命令自动补全，使用tab键补全命令
- 命令长帮助，使用help [command]显示命令长帮助
- 长帮助补全，输入命令后双击tab键补全命令长帮助指令
- 快捷键，支持使用Ctrl + A~Z组合按键直接调用函数
- shell变量，支持在shell中查看和修改变量值，支持变量作为命令参数
- 登录密码，支持在shell中使用登录密码，支持超时自动锁定

## 移植

### 硬件

芯片：STM32F103
外设：uart1

软件：[MobaXterm]([MobaXterm free Xserver and tabbed SSH client for Windows (mobatek.net)](https://mobaxterm.mobatek.net/))

### 适配步骤

1. 定义shell对象
```c
SHELL_TypeDef shell;
```

2. 在`shell_cfg.h`中开启`SHELL_USING_TASK`配置

```c
/**
 * @brief 是否使用默认shell任务
 *        使能之后可以使用`shellTask()`建立shell任务，或者使用`shellTask()`进行轮询
 */
#define     SHELL_USING_TASK            1
```

3. 定义读、写一个字符的函数
```c
/* 接收这里用了环形缓冲区 */
signed char uart1_shell_read(char *data)
{
    if (rb_used_len(&uart1_rb) > 0)
    {
        rb_read(&uart1_rb, (unsigned char*)data, 1);
        return 0;
    }
    return -1;
}

void uart1_shell_write(const char data)
{
    USART_SendData(USART1, data);
    while (USART_GetFlagStatus(USART1, USART_FLAG_TC) == RESET);
}
```

4. 初始化shell对象

```c
void shell_init()
{
    uart1_shell.read = uart1_shell_read;
    uart1_shell.write = uart1_shell_write;
    shellInit(&uart1_shell);
}
```

5. 调用shell任务
```c
void task_shell()
{
    shellTask(&uart1_shell);
}
```

