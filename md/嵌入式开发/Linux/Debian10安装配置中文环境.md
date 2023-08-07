# Debian10安装配置中文环境

## 安装中文

```shell
sudo apt install locales
sudo apt install ttf-wqy-zenhei
```

## 配置默认中文
```shell
sudo dpkg-reconfigure locales
```

## 安装中文输入法

```shell
sudo apt install fcitx fcitx-tools fcitx-config* fcitx-module* fcitx-ui-* presage
sudo sed -i 's/default/fcitx/g' /etc/X11/xinit/xinputrc
im-config -s fcitx
```
