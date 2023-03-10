# uFTP-嵌入式Linux下的FTPServer实现

## 介绍

uFTP服务器是一个开源的，可移植的，轻量级的FTP服务器，用C编写，适用于Unix / Linux和POSIX兼容操作系统。uFTP服务器与所有Linux发行版兼容，它可以在各种架构中运行，包括x86，x64，ARM等。

特点：

- 易于安装配置到arm-linux
- 实现了Server常用功能
- C语言编写
- 开源宽松的MIT协议

## 功能

1. 支持本地用户和虚拟用户登录，匿名登录暂时不支持（不过根据现有代码很快可以加上）
2. 支持主动模式和被动模式
3. 支持FTPS加密
4. 支持ASCII和二进制文件类型
5. 支持常用的上传、下载、查看目录等命令

## 适配

这个软件包的适配真的很简单，只需要改两个文件一个Makefile，一个uftpd配置文件。编译之后会生成一个可执行文件，启动这个程序后它会自动在可执行文件目录下寻找uftpd.cfg，读取了配置文件后执行相应服务。

1. Makefile改动
   
   ```makefile
   # 指定编译器
   CC=gcc
   # 选择是否支持大文件（2G以上）读写
   
   ENABLE_LARGE_FILE_SUPPORT=
   #TO ENABLE THE LARGE FILE SUPPORT UNCOMMENT THE NEXT LINE
   
   # ENABLE_LARGE_FILE_SUPPORT=-D LARGE_FILE_SUPPORT_ENABLED -D _LARGEFILE64_SOURCE
   
   # 选择是否支持SSL
   ENABLE_OPENSSL_SUPPORT=
   #TO ENABLE OPENSSL SUPPORT UNCOMMENT NEXT 2 LINES
   #ENABLE_OPENSSL_SUPPORT=-D OPENSSL_ENABLED
   #LIBS=-lpthread -lssl -lcrypto
   
   # 选择是否支持用主机本地的账户登录
   ENABLE_PAM_SUPPORT=
   PAM_AUTH_LIB=
   #TO ENABLE PAM AUTH UNCOMMENT NEXT TWO LINES
   #ENABLE_PAM_SUPPORT= -D PAM_SUPPORT_ENABLED
   #PAM_AUTH_LIB= -lpam
   ```

2. uftpd.cfg改动

```textile
# 最多支持连接的FTPClient数量

MAXIMUM_ALLOWED_FTP_CONNECTION = 10

# 是否只能执行一个Server进程

SINGLE_INSTANCE = true

# 是否采用守护进程模式

DAEMON_MODE = false

# 无操作超时时间

IDLE_MAX_TIMEOUT = 3600

# 每个IP做多可连接数量

MAX_CONNECTION_NUMBER_PER_IP = 10

# 每个IP最多可登录数量

MAX_CONNECTION_TRY_PER_IP = 10

# SSL证书目录

CERTIFICATE_PATH=/etc/uFTP/cert.pem
PRIVATE_CERTIFICATE_PATH=/etc/uFTP/key.pem

# 是否采用主机本地账户

#Enable system authentication based on /etc/passwd
#and /etc/shadow
ENABLE_PAM_AUTH = false

# 随机端口范围

RANDOM_PORT_START = 10000
RANDOM_PORT_END   = 50000

# 控制端口

FTP_PORT = 21
#TCP/IP PORT SETTINGS (DEFAULT 21)

# 账户配置 START FROM USER 0 TO XXX

USER_0 = tang
PASSWORD_0 = tang
HOME_0 = /var/user/
GROUP_NAME_OWNER_0 = root
USER_NAME_OWNER_0 = root

# USER_1 = apache

# PASSWORD_1 = apachePassword

# HOME_1 = /var/www/html/

# GROUP_NAME_OWNER_1 = www-data

# USER_NAME_OWNER_1 = www-data
```

## 资料

项目地址:<https://github.com/kingk85/uFTP>
项目文档:<https://www.uftpserver.com/wiki/opensource-ftp-server>

```

```


