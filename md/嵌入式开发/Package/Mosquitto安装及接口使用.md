# Mosquitto安装及接口使用

## 安装

```bash
sudo apt-get install mosquitto  #安装后才能运行MQTT broker
sudo apt-get install mosquitto-clients # 安装后才能命令行执行client功能
sudo apt-get install libmosquitto-dev  #安装开发包才能在程序中调用
```

这个网站可以查看安装包具体文件列表:<https://packages.ubuntu.com/bionic/libmosquitto-dev>

## API

官方API手册：<https://mosquitto.org/api/files/mosquitto-h.html>

1. 初始化资源和注销资源

```
int mosquitto_lib_init(void);//必须初始化资源后才能使用其它接口
int mosquitto_lib_cleanup(void);
```

2. 创建客户端和注销客户端

```
struct mosquitto *mosquitto_new(const char *id,
                                bool clean_session,
                                void *obj);

void mosquitto_destroy(struct mosquitto *mosq);
```

> obj:用户参数，可以传递给后面的任意回调函数

3. 设置参数

```
int mosquitto_username_pw_set(struct mosquitto *mosq,
                                const char *username,
                                const char *password);

int mosquitto_opts_set(struct mosquitto *mosq,
                        enum mosq_opt_t option,
                        void *value);
```

4. 设置回调

```
mosquitto_log_callback_set();//设置日志回调
mosquitto_connect_callback_set();//连接事件回调
mosquitto_disconnect_callback_set();//断连事件回调
mosquitto_publish_callback_set();//发布事件回调
mosquitto_subscribe_callback_set();//订阅事件回调
mosquitto_unsubscribe_callback_set();//取消订阅事件回调
mosquitto_message_callback_set();//接收broker消息回调
```

5. 连接和断开连接

```
int mosquitto_connect(struct mosquitto *mosq, 
                        const char *host, int port, int keepalive);

int mosquitto_disconnect(struct mosquitto *mosq);

int mosquitto_reconnect(struct mosquitto *mosq);
```

6. 发布和订阅

```
int mosquitto_publish(struct mosquitto *mosq,
                        int *mid,
                        const char *topic,
                        int payloadlen,
                        const void *payload,
                        int qos,
                        bool retain);


int mosquitto_subscribe(struct mosquitto *mosq,
                        int  *mid,
                        const char *sub,
                        int qos);

int mosquitto_unsubscribe(struct mosquitto *mosq,
                            int  *mid,
                            const char *sub);
```

7. 网络循环

loop接口需要在进程存在时一直调用，它会自动去处理那些发布和接收事件，自动重连。有两种方式使用，一种是调用mosquitto_loop_forever进程会阻塞在这里，一种是调用mosquitto_loop_start创建新的线程处理内部事件。

```
int mosquitto_loop_forever(struct mosquitto *mosq,
                            int timeout,
                            int max_packets);

int mosquitto_loop_start(struct mosquitto *mosq);
int mosquitto_loop_stop(struct mosquitto *mosq, bool force);
```

> force：设置为 true 以强制取消线程。如果为 false，则mosquitto_disconnect必须已被调用。

## Demo

Client使用流程：初始化资源->创建客户端->设置参数->设置回调->刷新loop

```c
#include<stdio.h>
#include<stdlib.h>
#include<string.h>
#include<mosquitto.h>

#define HOST "broker-cn.emqx.io"
#define PORT  1883

static void my_log_callback(struct mosquitto *mosq, void *obj, int level, const char *str)
{
    printf("LOG CALLBACK:");
    printf("%s\n", str);
}

static void my_connect_callback(struct mosquitto *mosq, void *obj, int result)
{
    printf("CONNECT CALLBACK:");
    printf("CONNECT:%s\n", mosquitto_connack_string(result));
}

static void my_disconnect_callback(struct mosquitto *mosq, void *obj, int rc)
{
    printf("DISCONNECT CALLBACK:");
    printf("DISCONNECT:%s\n", mosquitto_connack_string(rc));
}

static void my_publish_callback(struct mosquitto *mosq, void *obj, int mid)
{
    printf("PUBLISH CALLBACK:%d\n",mid);
}

static void my_subscribe_callback(struct mosquitto *mosq, void *obj, int mid, int qos_count, const int *granted_qos)
{
    printf("SUBSCRIBE CALLBACK:%d|%d\n",mid,qos_count);
    for(int i=0; i<qos_count; i++)
    {
        printf("%d \n",granted_qos[i]);
    }

}

static void my_message_callback(struct mosquitto *mosq, void *obj, const struct mosquitto_message *message)
{
    printf("MESSAGE CALLBACK\n");
    printf("Topic:%s,",message->topic);
    printf("Len:%d\n",message->payloadlen);
    printf("Payload:%s\n",(char*)message->payload);
}

int main()
{
    mosquitto_lib_init();

    int ver=0,major=0,minor=0,revision=0;
    ver = mosquitto_lib_version(&major,&minor,&revision);
    printf("VER:%d,major:%d,minor:%d,rev:%d\n",ver,major,minor,revision);

    struct mosquitto *mosq = NULL;
    mosq = mosquitto_new("mosquitto123", true, NULL) ;
    if(!mosq)
    {
        perror("NEW mosquitto error");
        mosquitto_lib_cleanup();
        return 1;
    }
    else
    {
        printf("NEW CLIENT!\n");
    }

    mosquitto_username_pw_set(mosq, "user", "pw123456");

    // mosquitto_log_callback_set(mosq, my_log_callback);
    mosquitto_connect_callback_set(mosq, my_connect_callback);
    // mosquitto_disconnect_callback_set(mosq, my_disconnect_callback);
    mosquitto_publish_callback_set(mosq, my_publish_callback);
    mosquitto_subscribe_callback_set(mosq, my_subscribe_callback);
    mosquitto_message_callback_set(mosq, my_message_callback);

    int rc = 0;
    rc = mosquitto_connect(mosq, HOST, PORT, 60);
    if(rc)
    {
        printf("Error: %s\n", mosquitto_strerror(rc));
        return 1;
    }
    else
    {
        printf("Connect CLIENT!\n");
    }

    rc = mosquitto_subscribe(mosq, NULL, "mosquitto/test", 1);
    printf("SUB: %s\n", mosquitto_strerror(rc));

    rc = mosquitto_publish(mosq, NULL, "mosquitto/test1", 5, "hello", 1, false);
    printf("PUB: %s\n", mosquitto_strerror(rc));

    mosquitto_loop_forever(mosq, -1, 1);

    mosquitto_destroy(mosq);
    mosquitto_lib_cleanup();

    return 0;
}
```
