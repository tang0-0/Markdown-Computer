# HTTP协议理解

## 1.特点

1. HTTP协议是一种应用层协议，基于TCP连接。
2. 基于请求/响应模型
3. 无连接，即每次连接只处理一个请求。服务器处理完客户的请求，并收到客户的应答后，即断开连接。采用这种方式可以节省传输时间。
4. 无状态，即协议本身并不保留之前一切的请求或响应报文的信息。

## 2.报文组成

### 请求报文
- 请求行：包含请求方法和协议版本
- 请求头
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
- 响应头
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

## 3.请求方法

### 1. `GET`

请求获取Request-URI所标识的资源

### 2. `POST`

向指定资源提交数据进行处理请求（例如提交表单或者上传文件）。数据被包含在请求体中。POST请求可能会导致新的资源的建立和/或已有资源的修改。

### 3. `HEAD`

向服务器索与GET请求相一致的响应，只不过响应体将不会被返回。这一方法可以再不必传输整个响应内容的情况下，就可以获取包含在响应小消息头中的元信息。

### 4. `PUT`

向指定资源位置上传其最新内容

### 5. `DELETE`

请求服务器删除Request-URL所标识的资源

### 6. `TRACE`

回显服务器收到的请求，主要用于测试或诊断

### 7. `CONNECT`

HTTP/1.1协议中预留给能够将连接改为管道方式的代理服务器

### 8. `OPTIONS`

返回服务器支持的请求方法，也可以利用向web服务器发送‘*’的请求来测试服务器的功能性

