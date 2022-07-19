# pyqt5 + vscode 环境搭建

>Version:
>python:v3.8.5
>pyqt5:v5.15.2
>pyqt-tools:v5.15.2.3.0.2
>pyserial:v3.5

# 1.安装`pyqt5`

```
pip install PyQt5
```

# 2.安装`pyqt-tools`

主要是用qt designer来设计UI

```
pip install PyQt5-tools
```

# 3.安装`pyserial`

```python
pip install pyserial
```


# 4.安装vscode插件`pyqt integration`
vscode->setting->搜索pyqt,对pyuic5的路径以及QT designer的路径进行设置。（我的pyuic5是默认的没有改，designer为空，所以对designer路径进行配置）

```
D:\Python38\Lib\site-packages\qt5_applications\Qt\bin\designer
```

换源：
pip install xxx -i https://pypi.mirrors.ustc.edu.cn/simple/
pip config set global.index-url https://pypi.mirrors.ustc.edu.cn/simple/



