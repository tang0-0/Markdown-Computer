# 免费MQTT测试服务器

## 1. test.mosquitto.org

> 官网：https://test.mosquitto.org/

服务器侦听以下端口：
1883 ： MQTT， 未加密， 未经身份验证
1884 ： MQTT， 未加密， 已通过身份验证
8883：  MQTT， 加密， 未经身份验证
8884 ： MQTT，已加密，需要客户端证书
8885 ： MQTT， 加密， 已通过身份验证
8886 ： MQTT， 加密， 未经身份验证
8887：  MQTT，已加密，服务器证书故意过期
8080 ： MQTT over WebSockets， 未加密， 未经身份验证
8081 ： MQTT over WebSockets， 已加密， 未经身份验证
8090 ： MQTT over WebSockets， 未加密， 已通过身份验证
8091 ： MQTT over WebSockets， 加密， 已通过身份验证


## 2. broker-cn.emqx.io

> 官网：https://www.emqx.com/zh/mqtt/public-mqtt5-broker

TCP 端口:1883
Websocket 端口:8083
TCP/TLS 端口:8883
Websocket/TLS 端口:8084

## 3. broker.hivemq.com

> 官网：https://www.hivemq.com/public-mqtt-broker/

TCP Port: 1883
Websocket Port: 8000
