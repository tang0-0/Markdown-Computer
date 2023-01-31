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

## 开发流程

1. 通过SSH登录虚拟机，进入代码文件

2. 修改代码后，在对应目录下执行`make`

3. 网关系统管理中打开开发者模式，通过SFTP登录网关

4. 然后将可执行文件通过SFTP上传到网关中，上传的文件会在`/var/user/app/device_supervisor/src/`对应目录下

5. 修改设备中执行文件的权限`u+x`,将`/var/user/lib:/var/user/app/device_supervisor/lib`添加LD_LIBRARY_PATH

6. 可以在该目录下手动执行进程，不过重启后并不会自动执行

7. 把执行文件复制到`/var/user/app/device_supervisor`下，执行文件才会在开机时自动执行
