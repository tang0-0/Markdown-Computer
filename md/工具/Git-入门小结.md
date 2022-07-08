![img](https://imgconvert.csdnimg.cn/aHR0cDovL3d3dy5ydWFueWlmZW5nLmNvbS9ibG9naW1nL2Fzc2V0LzIwMTUvYmcyMDE1MTIwOTAxLnBuZw?x-oss-process=image/format,png)
#### 系列文章
[Git-入门小结](https://blog.csdn.net/lblmlms/article/details/105600621)
[Git-分支](https://blog.csdn.net/lblmlms/article/details/107251420)
[Git-常用命令](https://blog.csdn.net/lblmlms/article/details/107834005)
[Git-注册远程仓库](https://blog.csdn.net/lblmlms/article/details/107554350)

# Git安装及上手
## 一、安装

官网下载安装，我是选择一路默认下去，具体步骤参考此篇博客：<https://www.cnblogs.com/xueweisuoyong/p/11914045.html>

安装包：<https://download.csdn.net/download/lblmlms/12598938>

## 二、上手

### 参考：<https://zhuanlan.zhihu.com/p/30044692>

1. ### 注册
	桌面右键->`Git Bash Here`->进入命令行

	 ```
	git config --global user.name "your name"//提交用户名
	git config --global user.email "youremail@"//提交邮件地址
	 ```
	   

2. ### 升级

   ```
   git update-git-for-windows
   ```
   
3. ### 本地创建仓库

   ```c++
   git init //在当前目录新建一个仓库
   git init name //新建一个目录将其初始化为仓库
   ```
