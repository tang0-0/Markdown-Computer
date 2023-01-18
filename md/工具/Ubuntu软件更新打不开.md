# Ubuntu软件中心打不开

## 环境

VirtualBox6.1.40+Ubuntu16.04

## 原因

安装`Python3.7`后将`python3`命令指向了`python3.7`，但是貌似这个软件更新必须用系统自带的`python3.5`打开。

## 解决

重新建立软连接，用完再设置回去。

```bash
sudo rm -r /usr/bin/python3
sudo ln -s /usr/bin/python3.5 /usr/bin/python3


sudo rm -r /usr/bin/python3
sudo ln -s /usr/local/bin/python3.7.2/bin/python3.7 /usr/bin/python3
```
