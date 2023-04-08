# LWIP-TCP心跳机制

## 简介

在长连接下，可能很长一段时间都没有数据往来。理论上说，这个连接是一直保持连接的，但是实际情况中，如果中间节点出现什么故障是难以知道的。更致命的是，有的节点（防火墙）会自动把一定时间之内没有数据交互的连接给断掉。这个时候，就可以使用心跳包，来维持长连接以及保活

心跳机制就是每隔几分钟发送一个固定信息给服务端，服务端收到后回复一个固定信息如果服务端几分钟内没有收到客户端信息则视客户端断开。发包方可以是客户也可以是服务端，具体看哪边实现更方便合理

## 设置

### 1. 开启宏(`opt.h`)

```c
/**
 * LWIP_TCP_KEEPALIVE==1: Enable TCP_KEEPIDLE, TCP_KEEPINTVL and TCP_KEEPCNT
 * options processing. Note that TCP_KEEPIDLE and TCP_KEEPINTVL have to be set
 * in seconds. (does not require sockets.c, and will affect tcp.c)
 */
#if !defined LWIP_TCP_KEEPALIVE || defined __DOXYGEN__
#define LWIP_TCP_KEEPALIVE              1
#endif

```

### 2. 配置

```c
/*
TCP_KEEPCNT 关闭一个非活跃连接之前的最大重试次数。
TCP_KEEPIDLE 设置连接上如果没有数据发送的话，多久后发送keepalive探测分组，单位是秒
TCP_KEEPINTVL 前后两次探测之间的时间间隔，单位是秒
*/

int keepalive = 1;//非0值，开启keepalive属性
int keepidle = 5*60;//5min内没有数据交互就发送心跳
int keepinterval = 60;//心跳发送间隔
int keepcount = 9;//重发次数

// 使能
lwip_setsockopt(client, SOL_SOCKET, SO_KEEPALIVE, (void *)&keepalive, 												sizeof(keepalive));
// 心跳间隔
lwip_setsockopt(client, IPPROTO_TCP, TCP_KEEPIDLE, (void *)&keepidle, 												sizeof(keepidle));
// 心跳超时
lwip_setsockopt(client, IPPROTO_TCP, TCP_KEEPINTVL, (void *)&keepinterval,
						sizeof(keepinterval));
// 重发次数
lwip_setsockopt(client, IPPROTO_TCP, TCP_KEEPCNT, (void *)&keepcount, 												sizeof(keepcount));


```

