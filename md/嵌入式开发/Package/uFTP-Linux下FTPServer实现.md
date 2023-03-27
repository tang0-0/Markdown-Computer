# uFTP-Linux下FTPServer实现

## 一、介绍

uFTP服务器是一个开源的，可移植的，轻量级的FTP服务器，用C编写，适用于Unix / Linux和POSIX兼容操作系统。uFTP服务器与所有Linux发行版兼容，它可以在各种架构中运行，包括x86，x64，ARM等。

特点：

- C语言编写
- 易于安装配置到arm-linux
- 实现了Server常用功能
- 开源宽松的MIT协议

功能：

- 支持使用本地账号登录
- 支持创建虚拟账号登录，匿名登录暂时不支持（不过根据现有代码很快可以加上）
- 支持SSL加密
- 支持主动模式和被动模式
- 支持ASCII和二进制文件类型
- 支持常用的上传、下载、查看和删除等命令

## 二、其它实现

1. vsftpd: <https://security.appspot.com/vsftpd.html>
2. pure-ftpd: <https://www.pureftpd.org/project/pure-ftpd/>
3. bftpd: <https://bftpd.sourceforge.net/>

## 三、移植

移植只需要改动两个文件：一个Makefile，一个uftpd配置文件。编译之后会生成一个可执行文件，启动这个程序后它会自动在可执行文件目录下寻找uftpd.cfg，读取了配置文件后执行相应服务。

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

2. 生成uftpd.cfg
   
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
   ```

3. 使用
   
   在可执行文件路径下写一份uftpd.cfg，然后`sudo ./uFTP`，即可执行。

## 资料

项目地址:<https://github.com/kingk85/uFTP>
项目文档:<https://www.uftpserver.com/wiki/opensource-ftp-server>

使用过程中发现源码有些小问题，更改了下:<https://github.com/tang0-0/uFTP>
