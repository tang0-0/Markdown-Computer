# Linux基础命令

## 文件管理

- **ls**：列出当前目录下的文件和子目录。
`-l`：以长格式显示文件信息，包括权限、所有者、大小等。
`-a`：显示所有文件，包括隐藏文件。
`-h`：以易读的方式显示文件大小（例如，1K，2M）。

- **cd**: 切换当前工作目录。
`cd /home`：切换到 /home 目录。

- **mkdir**: 创建新目录。
`mkdir /home/newdir`：在 /home 目录下创建 newdir 目录。

- **touch**: 创建空文件或者更新已有文件的时间戳。
`touch 123.txt`：创建空文件 123.txt。

- **rm**: 删除文件或目录。
`-r`：递归删除目录及其内容。

- **find**: 在文件系统中搜索文件。
`find /home/ -name 123`: 在 /home 目录下搜索文件名为 123 的文件。

- **mv**: 移动或重命名文件或目录。
`mv oldname newname` : 重命名文件或目录。
`mv sourcefile destination`: 移动文件或目录。

- **cp**: 复制文件或目录。
`cp /home/123.txt /tmp`: 将 /home/123.txt 复制到 /tmp 目录下。

- **cat**: 查看文件内容。
`cat /home/123.txt`: 查看 /home/123.txt 文件的内容。

- **tail**：用于显示文件的末尾部分，通常用于查看日志文件。
`-f`：动态监视文件，不断显示新添加到文件中的内容。

## 软件安装

apt 命令是一个用于在 Debian 和 Ubuntu 系统中进行包管理操作的强大工具。它是 Advanced Package Tool（高级包管理工具）的缩写，提供了一种方便的方式来安装、升级、删除和管理软件包。

- 更新软件包列表：`sudo apt update`
- 安装软件包：`sudo apt install package_name`
- 删除软件包：`sudo apt remove package_name`
- 删除软件包及配置：`sudo apt purge package_name`
- 搜索软件包：`apt search search_term`
- 显示软件包信息：`apt show package_name`
- 列出已安装的软件包：`apt list --installed`
- 修改软件源头：`sudo vim /etc/apt/sources.list`

## 权限管理

- **chmod**：修改文件或目录的权限。
`chmod +rwx filename`：更改文件的权限为读写执行（用户、组和其他用户都有完全权限）
`chmod -x filename`：移除文件的执行权限
`chmod 0644 filename`：将文件的权限设置为特定的数字模式

- **chown**：修改文件或目录的所有者。
`chown newuser filename`：将文件的所有者更改为新用户
`chown -R newuser directoryname`：更改目录及其内容的所有者为特定用户（递归操作）

- **chgrp**：修改文件或目录的所属组。
`chgrp newgroup filename`：更改文件的所属组为特定组
`chgrp -R newgroup directoryname`：更改目录及其内容的所属组为特定组（递归操作）

- **sudo**：以超级用户身份执行命令。
`sudo command`：以 root 身份执行命令
`sudo -u username command`：以其他用户身份执行命令
`sudo -i`：切换到 root 用户

- **password**：更改用户密码。
`sudo passwd username`：更改指定用户密码

## 网络管理

- **ifconfig**：显示网络接口信息。
`ifconfig`：显示所有网络接口的信息，包括接口名称、IP 地址、MAC 地址等。
`ifconfig eth0`：显示特定网络接口（例如，eth0）的详细信息。

- **ping**：测试网络连接。
`ping hostname`：向指定主机（使用主机名或 IP 地址）发送 ICMP 包，以测试主机的可达性和网络延迟。

- **tcpdump**：抓取网络数据包。
-`i eth0`：指定要抓取的网络接口。
-`w filename`：将抓取的数据包写入文件。
-`c num`：指定抓取的数据包数量。
`tcpdump -i eth0 -w filename -c 100`：抓取 eth0 网络接口的前 100 个数据包，并将其写入文件。

- **scp**：在本地主机和远程主机之间复制文件。
`scp ./123.txt username@remote_host:/home`：将本地文件复制到远程主机。
`scp username@remote_host:./123.txt /home`：从远程主机复制文件到本地主机。

- **netstat**：显示网络连接、路由表和网络接口信息。
`netstat -tuln`：显示所有 TCP 和 UDP 网络连接。
`netstat -r`：显示系统的路由表信息。

- **traceroute**：显示数据包到达目标主机所经过的路由。
`traceroute hostname`：跟踪数据包到达目标主机的路径，显示每一跳的 IP 地址。

- **wget**：从网络下载文件。
`wget URL`：下载指定 URL 的文件到当前目录。

- **ssh**：远程登录到主机。
`ssh username@hostname`：使用 SSH 协议远程登录到目标主机。
`ssh -p port_number username@hostname`：指定 SSH 服务器的端口号。

- **telnet**：远程登录到主机。
`telnet hostname`：使用 Telnet 协议远程登录到目标主机。
`telnet hostname port_number`：指定 Telnet 服务器的端口号。

- **ftp**：使用 FTP 协议在本地主机和远程主机之间传输文件。
`ftp hostname`：连接到指定的 FTP 服务器。
`get remote_file`：从远程主机下载文件。
`put local_file`：上传本地文件到远程主机。

- **host**：查询 DNS 信息。
`host hostname`：查询指定主机名的 DNS 信息，包括 IP 地址解析。


## 进程管理

- **ps**：显示当前运行的进程。
`ps aux`：显示所有进程的详细信息，包括 CPU 和内存占用情况。

- **pstree**：以树状图显示进程之间的关系。

- **kill**：终止进程。
`kill -9 pid`：强制终止进程。

- **killall**：终止指定名称的进程。
`killall processname`：终止所有名称为 processname 的进程。

## 系统管理

- **uname**：显示系统信息。
`uname -a`：显示所有系统信息。

- **uptime**：显示系统的运行时间和平均负载。

- **top**：显示系统中运行的进程和资源占用情况。

- man：显示命令的帮助信息。
`man top`：显示 top 命令的帮助信息。

- **systemctl**：管理系统服务。
启动服务：`systemctl start service_name`
停止服务：`systemctl stop service_name`
重启服务：`systemctl restart service_name`
显示服务状态：`systemctl status service_name`
设置服务开机自启动：`systemctl enable service_name`
取消服务开机自启动：`systemctl disable service_name`
重新加载服务配置：`systemctl reload service_name`

## 存储管理

- **df**：显示文件系统的磁盘使用情况。
`df -h`：以易读的方式显示磁盘使用情况。

- **du**：显示目录或文件的磁盘使用情况。
`du -h`：以易读的方式显示当前目录的磁盘使用情况。

- **mount**：挂载文件系统。
`mount /dev/sdb1 /mnt`：将 /dev/sdb1 挂载到 /mnt 目录。

- **umount**：卸载文件系统。
`umount /mnt`：卸载 /mnt 目录。

- **frree**：显示内存使用情况。
`free -h`：以易读的方式显示内存使用情况。

## vim编辑器

Vim是一款常用的文本编辑器，它可以在终端中使用。它有两种模式：命令模式和编辑模式。

- 按下`i`键进入编辑模式，按下`Esc`键退出编辑模式进入命令模式。
- 按下`:`键进入命令模式，然后输入命令。
- 进入Vim编辑器：在终端中输入`vim`命令，后跟要编辑的文件名，如：vim /home/123.txt
- 插入文本：按下`i`键进入插入模式，此时可以输入文本。
- 保存文件：按下`Esc`键退出插入模式，然后输入`:wq`命令保存文件并退出Vim编辑器。
- 不保存文件退出：按下`Esc`键退出插入模式，然后输入`:q!`命令不保存文件并强制退出Vim编辑器。
- 移动光标：使用`h、j、k、l`键分别向左、下、上、右移动光标。
- 删除字符：在命令模式下按下`x`键可以删除光标所在位置的字符。
- 撤销操作：在命令模式下按下`u`键可以撤销上一次操作。
- 查找文本：在命令模式下输入`/`加上要查找的文本，如：/hello，然后按下回车键可以查找文本。