# AWS-IOT-SDK 调试

## 1.接收数据量大的消息出现异常

首先看下buf的长度是否设置足够，`AWS_IOT_MQTT_RX_BUF_LEN ` 
然后看下接收处理的timeout时间是否足够，`aws_iot_mqtt_yield(AWS_IoT_Client *pClient, uint32_t timeout_ms)`

## 2.MQTT_RX_MESSAGE_PACKET_TYPE_INVALID_ERROR

对心跳以及接收topic的处理都在`aws_iot_mqtt_yield`这个函数里，这个函数要及时调用并且timeout时间要给够

## 3.对于shadow的另一种连接方式

可以不用SDK中的shadow接口，直接用订阅/发布的接口
比如说要请求最新的shadow内容可以通过先向`ShadowTopicPrefix/get`发布一个空消息，接着`ShadowTopicPrefix/get/accepted`就会收到返回的shadow内容。job也可以用这种方式。
> 官方文档：https://docs.amazonaws.cn/iot/latest/developerguide/device-shadow-mqtt.html

