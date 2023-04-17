# Eu63-Collecter Euromap63协议采集接口实现

>仓库地址：<>

## 特点

1. C语言编写，无外部库依赖
2. 支持Connect、Report、Set命令

## 实现思路

1. 整个协议交互是基于对共享文件的操作，本组件只实现文件操作，不包含网络传输部分
2. 首先创建一个共享文件夹，然后在里面为每个注塑机建立单独的文件夹
3. 每个请求分为以下步骤：
    - 写会话层文件
    - 写表示层文件
    - 等待注塑机删除会话请求文件
    - 读取会话响应文件
    - 读取表示层响应文件
    - 读取应用层文件（如果有）
4. 文件读写采用C语言标准库接口，监听文件是否删除采用Linux系统的inotify机制
5. 参数的传入传出采用链表结构存储

## 使用例程

每隔一分钟发起一个Report请求，每次请求都包含Connect命令。

```c

#include <stdio.h>
#include <string.h>
#include <unistd.h>
#include "../Src/eu63_collecter.h"


static char *param_name[] = {"ABC001", "ABC002", "ABC003", "ABC004"};
static void print_param_value(eu63_param_list *list)
{
    ty_list_t *pos, *n;
    eu63_report_param *param;

    ty_list_for_each_entry_safe(param, pos, n, &(list->param_head), param_node)
    {
        printf("%s value:%s\n", param->name, param->value);
    }
}

int main(int argc, char *argv[])
{
    int ret = -1;
    eu63_collecter *collecter = NULL;
    eu63_param_list *list = NULL;

    ret = eu63_create_share_folder();
    if (ret)
    {
        printf("EU63 create share folder failed\n");
        return -1;
    }

    collecter = eu63_collecter_create("IMM01");
    if (!collecter)
    {
        printf("EU63 create collecter failed\n");
        return -2;
    }

    ret = eu63_create_imm_folder(collecter);
    if (ret)
    {
        printf("EU63 create imm folder failed\n");
        ret = -3;
        goto _error;
    }

    list = eu63_create_param_list();
    if (!list)
    {
        printf("EU63 create param list failed\n");
        ret = -4;
        goto _error;
    }

    eu63_report_param *param = NULL;
    for (int i = 0; i < 4; i++)
    {
        param = eu63_create_param();
        if (!param)
        {
            continue;
        }

        strncpy(param->name, param_name[i], sizeof(param->name));
        ty_list_append_head(&list->param_head, &param->param_node);
        list->req_count++;
        printf("Append %s to param list\n", param->name);
    }

    if (0 == list->req_count)
    {
        printf("EU63 param list is null\n");
        ret = -5;
        goto _error;
    }

    while (1)
    {
        printf("REQ report file\n");
        ret = eu63_execute_report_req(collecter, list);
        if (!ret)
        {
            print_param_value(list);
        }
        else
        {
            printf("EU63 REQ report failed\n");
        }

        sleep(60);
    }

_error:
    if (collecter)
    {
        eu63_collecter_free(collecter);
    }
    if (list)
    {
        eu63_free_param_list(list);
    }

    return ret;
}

```