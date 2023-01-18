# 开发环境

## 环境

1. VirtualBox
2. Ubuntu16.04
3. VScode

## VScode安装插件

### Remote-SSH

1. 新建一条设置
   
   Host 连接的主机的名称，可自定
   
   Hostname 远程主机的IP地址
   
   User 用于登录远程主机的用户名
   
   Protocol 不知道什么意思，默认2
   
   ![](C:\Users\ta\AppData\Roaming\marktext\images\9c6d7d4f5da0a76671001302cc181f4fd534c5c6.png)

2. 连接虚拟机中的Ubuntu
   
   ![](C:\Users\ta\AppData\Roaming\marktext\images\4988560fb2fbd7a848691f90b211a48e78926178.png)

3. 输入Ubuntu密码登录
   
   ![](C:\Users\ta\AppData\Roaming\marktext\images\2023-01-18-17-50-27-image.png)

### SFTP

1. 新建一条设置
   
   ![](C:\Users\ta\AppData\Roaming\marktext\images\2023-01-18-17-56-11-image.png)
   
   ```json
   {
       "name": "本地文件夹名称（可自定义）",
       "host": "ip或域名",
       "protocol": "协议：[sftp/ftp]默认ftp",
       "port": 22,
       "username": "username",
       "password":"password",
       "remotePath": "远程文件夹地址，默认/",
       "context": "本地文件夹地址，默认为vscode工作区根目录",
       "uploadOnSave": true,
       "downloadOnOpen":false,
       "ignore": [
           "**/.vscode/**",
           "**/.git/**",
           "**/.DS_Store"
       ],
       "watcher": {
           "files": "*",
           "autoUpload": false,
           "autoDelete": false
       }
   }
   ```

2. 登录设备的SFTP，输入密码
   
   ![](C:\Users\ta\AppData\Roaming\marktext\images\2023-01-18-18-11-22-image.png)


