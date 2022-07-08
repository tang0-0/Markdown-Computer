# Git-分支

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200710151603797.png#pic_center)

参考：[https://www.liaoxuefeng.com/wiki/896043488029600](https://www.liaoxuefeng.com/wiki/896043488029600)

#### 系列文章
[Git-入门小结](https://blog.csdn.net/lblmlms/article/details/105600621)
[Git-分支](https://blog.csdn.net/lblmlms/article/details/107251420)
[Git-常用命令](https://blog.csdn.net/lblmlms/article/details/107834005)
[Git-注册远程仓库](https://blog.csdn.net/lblmlms/article/details/107554350)

## 1.概念
先说个情景，有个小组正在做一个项目，代码已经写了一大半了，这时候要加一个功能。这个功能在前面代码的基础上就可以加上去，就是有些知识点没学到需要学一下。这时候一般有两种解决策略：一种是三个人一起来写这个新功能，单线作战先解决这个功能再继续项目；另一种是多线作战，再分一些人在现有代码的基础上去做新功能，之后再将功能合并起来。

有些情况下第一种策略更高效，有时候第二种策略更高效。Git里的分支就是基于第二种策略做的一个功能。

## 2.创建分支

```c
git branch dev//创建一个名字叫dev的分支
git branch -d dev//删除一个名叫dev的分支
git branch --all//查看所有分支，包括服务器上的分支
```

## 3.切换分支

```C
git checkout dev
git switch dev//这两个命令都可以切换分支
git switch -c dev//创建并切换到dev分支
git checkout origin "origin/dev"//切换到服务器上的dev分支
```



## 4.合并分支

```c
git switch dev//切换到dev分支
git merge bug//将bug分支合并到dev分支
git add .
git commit -m "bug into dev"//提交这次合并
```

## 5.举个栗子

- 查看、切换分支
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200814135708622.jpg?type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2xibG1sbXM=,size_16,color_FFFFFF,t_70#pic_center)

- 在`bug`分支的`try0.txt`文件的最后一行添加**bug=10**
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200814135738381.png?type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2xibG1sbXM=,size_16,color_FFFFFF,t_70#pic_center)

- 切换到`dev`分支
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200814135758286.png#pic_center)

- 在`dev`分支的`try0.txt`文件的最后一行添加**dev=10**
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200814135806976.png?type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2xibG1sbXM=,size_16,color_FFFFFF,t_70#pic_center)

- 合并分支，发生冲突
 ![在这里插入图片描述](https://img-blog.csdnimg.cn/20200710151007255.png#pic_center)
- 在编辑器（`VC code`）里修改代码，解决冲突
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200710151027230.png?#pic_center)
- 提交合并
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200710151038601.png?#pic_center)
