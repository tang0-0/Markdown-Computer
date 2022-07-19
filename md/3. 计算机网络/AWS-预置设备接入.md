# AWS-即时预置方式接入

>参考文档：<https://docs.amazonaws.cn/iot/latest/developerguide/iot-provision.html>

## 一、总体流程

1. 使用预置证书去注册事物的设备证书和私钥
2. 预置事物，当设备预置流程完成时，该证书的状态将更改为 ACTIVE
3. 使用注册得来的设备证书、私钥和本来的CA证书连接平台

## 二、注册证书

1. 连接AWS
2. 向`$aws/certificates/create/payload-format `上发布带有空消息负载的消息
	>payload-format
   消息负载格式为 cbor 或 json。
   如格式为json，topic为`$aws/certificates/create/json`
   
3. 订阅 `$aws/certificates/create/payload-format/accepted`

   >payload-format
   消息负载格式为 cbor 或 json。
   如格式为json，topic为`$aws/certificates/create/json/accepted`
   {
   "certificateId": "string",
       "certificatePem": "string",
       "privateKey": "string",
       "certificateOwnershipToken": "string"
       }
   certificateId:证书 ID。
   certificatePem:PEM 格式的证书数据。
   privateKey:私有密钥。
   certificateOwnershipToken:在预置期间证明证书所有权的令牌。
   
4. 要接收错误响应，请订阅 `$aws/certificates/create/payload-format/rejected`
   >payload-format
   消息负载格式为 cbor 或 json。
   如格式为json，topic为`$aws/certificates/create/json/rejected`
   {
   "statusCode": int,
       "errorCode": "string",
       "errorMessage": "string"
       }
   statusCode:状态代码。
   errorCode:错误代码。
   errorMessage:错误消息。
   

## 三、预置事物

1. 在 `$aws/provisioning-templates/templateName/provision/payload-format` 上发布消息
   >
   payload-format:消息负载格式为 cbor 或 json。
   templateName:预置模板名称。
   {
    "certificateOwnershipToken": "string",
    "parameters": {
        "string": "string",
        ...
    }
	}
   certificateOwnershipToken:证明证书所有权的令牌。令牌在您通过 MQTT 创建证书时由 Amazon IoT 时生成。
   parameters:可选。来自设备的键/值对由预先预置挂钩用于评估注册请求。

2. 订阅 `$aws/provisioning-templates/templateName/provision/payload-format/accepted`
	>payload-format:消息负载格式为 cbor 或 json。
	templateName:预置模板名称。
	{
    "deviceConfiguration": {
        "string": "string",
        ...
    },
    "thingName": "string"
	}
	deviceConfiguration:在模板中定义的设备配置。
	thingName:预置期间创建的 IoT 事物的名称。
	
3. 要接收错误响应，请订阅 `$aws/provisioning-templates/templateName/provision/payload-format/rejected`
	>payload-format:消息负载格式为 cbor 或 json。
	templateName:预置模板名称。
	{
    "statusCode": int,
    "errorCode": "string",
    "errorMessage": "string"
	}
	statusCode:状态代码。
	errorCode:错误代码。
	errorMessage:错误消息。

## 四、连接平台

使用`$aws/certificates/create/payload-format/accepted`返回的证书连接平台

