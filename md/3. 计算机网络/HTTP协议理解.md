# HTTP协议理解

>参考：<https://www.cnblogs.com/li0803/archive/2008/11/03/1324746.html>
>HTTPS连接过程:<https://blog.csdn.net/lblmlms/article/details/118875722>

## 1.特点

1. HTTP协议是一种应用层协议，基于TCP连接。
2. 基于请求/响应模型。
3. 无连接，即每次连接只处理一个请求。服务器处理完客户的请求，并收到客户的应答后，即断开连接。采用这种方式可以节省传输时间。
4. 无状态，即协议本身并不保留之前一切的请求或响应报文的信息。

## 2.报文组成

### 请求报文
- 请求行：包含请求方法和协议版本
- 消息报头
- 空行
- 请求体

```python
/* 请求行 */
GET /kongmin_123/article/details/82154780 HTTP/1.1
/* 请求头 */
Content-Type: text/plain
User-Agent: PostmanRuntime/7.28.1
Accept: */*
Cache-Control: no-cache
Postman-Token: 0b0f764d-7ae0-4b69-b009-445b50ca3b17
Host: blog.csdn.net
Accept-Encoding: gzip, deflate, br
Connection: keep-alive
Content-Length: 13
Cookie: dc_session_id=10_1627898159951.546697; dc_sid=c30647f4cb1a0387ba950356a1b79f36; uuid_tt_dd=10_19744463760-1627898159951-274682

/* 请求体 */
username:toda
```

### 响应报文
- 状态行：包含协议版本和状态码
- 消息报头
- 空行
- 响应体

```python
/* 状态行 */
HTTP/1.1 200 OK
/* 响应头 */
Server: openresty
Date: Mon, 02 Aug 2021 09:59:32 GMT
Content-Type: text/html;charset=utf-8
Transfer-Encoding: chunked
Connection: keep-alive
Keep-Alive: timeout=20
Vary: Accept-Encoding
Content-Language: en-US
Strict-Transport-Security: max-age= 31536000
Content-Encoding: gzip

/* 响应体 */
......

```

### 状态码

状态码有三位数字组成，第一个数字定义了响应的类别，且有五种可能取值：
`1xx`：指示信息--表示请求已接收，继续处理
`2xx`：成功--表示请求已被成功接收、理解、接受
`3xx`：重定向--要完成请求必须进行更进一步的操作
`4xx`：客户端错误--请求有语法错误或请求无法实现
`5xx`：服务器端错误--服务器未能实现合法的请求

常见状态代码
`200` OK      //客户端请求成功
`400` Bad Request  //客户端请求有语法错误，不能被服务器所理解
`401` Unauthorized //请求未经授权，这个状态代码必须和WWW-Authenticate报头域一起使用 
`403` Forbidden  //服务器收到请求，但是拒绝提供服务
`404` Not Found  //请求资源不存在，eg：输入了错误的URL
`500` Internal Server Error //服务器发生不可预期的错误
`503` Server Unavailable  //服务器当前不能处理客户端的请求，一段时间后可能恢复正常


## 3.消息报头

消息报头有四类：通用报头、请求报头、响应报头、实体报头。
每一个报头域都是由`名字+“:”+空格+值 `组成，消息报头域的名字是大小写无关的。

### 通用报头

通用头域包含请求和响应消息都支持的头域，通用头域包含Cache-Control、Connection、Date、Pragma、Transfer-Encoding、Upgrade、Via。对通用头域的扩展要求通讯双方都支持此扩展，如果存在不支持的通用头域，一般将会作为实体头域处理。

`Date`：表示消息产生的日期和时间。

`Connection`：允许发送指定连接的选项，例如指定连接是连续的，或者指定“close”选项，通知服务器，在响应完成后，关闭连接。

`Cache-Control`：用于指定缓存指令，缓存指令是单向的（响应中出现的缓存指令在请求中未必会出现），且是独立的（一个消息的缓存指令不会影响另一个消息处理的缓存机制）。

`Pragma`: 用来包含实现特定的指令，最常用的是Pragma:no-cache。在HTTP/1.1协议中，它的含义和Cache-Control:no-cache相同。


### 请求报头

请求报头用于请求报文里，通知服务器关于客户端请求的信息。

`Host（必需）`：用于指定被请求资源的主机和端口号，它通常从HTTP URL中提取出来的，eg：
我们在浏览器中输入：http://www.guet.edu.cn/index.html
浏览器发送的请求消息中，就会包含Host请求报头域，如下：
Host：www.guet.edu.cn
此处使用缺省端口号80，若指定了端口号，则变成：Host：www.guet.edu.cn:指定端口号。

`User-Agent`：向访问网站提供你所使用的浏览器类型、操作系统及版本、CPU 类型、浏览器渲染引擎、浏览器语言、浏览器插件等信息。

`Connection`：close表示每个每个请求都会重新建立TCP连接；keep-alive表示保持TCP连接，后续请求都在同一条tcp连接上。

`Accept`：用于指定客户端接受哪些类型的信息。eg：Accept：image/gif，表明客户端希望接受GIF图象格式的资源；Accept：text/html，表明客户端希望接受html文本。

`Accept-Encoding`：客户端可以接收的编码格式。

`Range`: 表示访问内容的范围，bytes＝100-200 表示访问100-200字节的内容。


### 响应报头

请求报头用于响应报文里，通知客户端关于服务器的信息。

`Location`：响应报头域用于重定向接受者到一个新的位置。Location响应报头域常用在更换域名的时候。

`Server`: 响应报头域包含了服务器用来处理请求的软件信息。与User-Agent请求报头域是相对应的。eg:Server：Apache-Coyote/1.1。

### 实体报头

实体报头HTTP header用来描述消息体内容。实体报头既可用于请求也可用于响应中。如Content-Length，Content-Language，Content-Encoding之类的报头都是实体报头。

`Content-Encoding`: 实体报头域被用作媒体类型的修饰符，它的值指示了已经被应用到实体正文的附加内容的编码，因而要获得Content-Type报头域中所引用的媒体类型，必须采用相应的解码机制。Content-Encoding这样用于记录文档的压缩方法，eg：Content-Encoding：gzip。

`Content-Language`: 实体报头域描述了资源所用的自然语言。没有设置该域则认为实体内容将提供给所有的语言阅读者。

`Content-Length`: 实体报头域用于指明实体正文的长度，以字节方式存储的十进制数字来表示。

`Content-Type`: 实体报头域用语指明发送给接收者的实体正文的媒体类型。eg：
Content-Type:text/html;charset=ISO-8859-1
Content-Type:text/html;charset=GB2312

`Last-Modified`: 实体报头域用于指示资源的最后修改日期和时间。


## 4.请求方法

### 1. `GET`

请求获取Request-URI所标识的资源。

### 2. `POST`

向指定资源提交数据进行处理请求（例如提交表单或者上传文件）。数据被包含在请求体中。POST请求可能会导致新的资源的建立和/或已有资源的修改。

### 3. `HEAD`

向服务器索与GET请求相一致的响应，只不过响应体将不会被返回。这一方法可以再不必传输整个响应内容的情况下，就可以获取包含在响应小消息头中的元信息。

### 4. `PUT`

向指定资源位置上传其最新内容。

### 5. `DELETE`

请求服务器删除Request-URL所标识的资源。

### 6. `TRACE`

回显服务器收到的请求，主要用于测试或诊断。

### 7. `CONNECT`

HTTP/1.1协议中预留给能够将连接改为管道方式的代理服务器。

### 8. `OPTIONS`

返回服务器支持的请求方法，也可以利用向web服务器发送‘*’的请求来测试服务器的功能性。

