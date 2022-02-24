#### 系列文章
[Git-入门小结](https://blog.csdn.net/lblmlms/article/details/105600621)
[Git-分支](https://blog.csdn.net/lblmlms/article/details/107251420)
[Git-常用命令](https://blog.csdn.net/lblmlms/article/details/107834005)
[Git-注册远程仓库](https://blog.csdn.net/lblmlms/article/details/107554350)

# Git-远程仓库

## 1.生成SSH KEY
```c
ssh-keygen -t rsa -C "youremail@xx.com"
//git里注册的邮箱，可以用git config --global user.email查看

Generating public/private rsa key pair.
Enter file in which to save the key 
(/c/Users/Administrator/.ssh/id_rsa)://按Enter
/c/Users/Administrator/.ssh/id_rsa already exists.
Overwrite (y/n)? y//这里我的文件夹里已经有SSH key了，命令行提示要不要重新写入

/**
*设置passphrase后，进行版本控制时，
*每次与远程仓库通信都会要求输入passphrase，
*这里我直接Enter不输入密码
**/
Enter passphrase (empty for no passphrase)://Enter
Enter same passphrase again://Enter

Your identification has been saved in /c/Users/Administrator/.ssh/id_rsa.
Your public key has been saved in /c/Users/Administrator/.ssh/id_rsa.pub.
The key fingerprint is:
SHA256:X.........................oo your_emial@examle.com
The key's randomart image is:
+---[RSA 2048]----+
|                 |
|       o .       |
|      = +        |
|     . * o       |
|  o o = S .      |
| = = = + .       |
|o B = + o        |
|o= = =B=.*       |
|E.o ++=@O.o      |
+----[SHA256]-----+


```

## 2.查看公钥
```c
cd ~/.ssh//进入.ssh文件夹
cat id_rsa.pub//查看公钥
```

## 3.与gitlab关联

![img](https://imgconvert.csdnimg.cn/aHR0cHM6Ly9pbWcyMDE4LmNuYmxvZ3MuY29tL2Jsb2cvNjc4Njc2LzIwMTkxMi82Nzg2NzYtMjAxOTEyMjYxNDQ2NTI4NTctODA5NzQ3NjA2LnBuZw?x-oss-process=image/format,png)

## 4.clone

```c
git clone URL
git clone -b dev URL//clone指定分支下代码
```

- 选好项目

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200724095301160.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2xibG1sbXM=,size_16,color_FFFFFF,t_70)

- clone到本地

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200724095210329.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2xibG1sbXM=,size_16,color_FFFFFF,t_70)


## 4.push and pull

```c
git fetch origin dev//从origin服务器更新代码，此时代码被更新到仓库
git pull origin dev//从origin服务器更新代码，此时代码被更新到工作区
git push origin dev//将仓库中dev分支代码推送到服务器分支上
```

fetch步骤：
- git fetch
- git merge
```c
//方法一
$ git fetch origin master //从远程的origin仓库的master分支下载代码到本地的origin master

$ git log -p master.. origin/master//比较本地的仓库和远程参考的区别

$ git merge origin/master//把远程下载下来的代码合并到本地仓库，远程的和本地的合并

//方法二
$ git fetch origin master:temp //从远程的origin仓库的master分支下载到本地并新建一个分支temp

$ git diff temp//比较master分支和temp分支的不同

$ git merge temp//合并temp分支到master分支

$ git branch -d temp//删除temp
```

```c

$ git fetch origin bug
remote:
remote: INFO: Your SSH key is expiring soon. Please generate a new key.
remote:
remote: Enumerating objects: 5, done.
remote: Counting objects: 100% (5/5), done.
remote: Compressing objects: 100% (3/3), done.
remote: Total 3 (delta 1), reused 0 (delta 0), pack-reused 0
Unpacking objects: 100% (3/3), 286 bytes | 4.00 KiB/s, done.
From gitlab.inhand.design:tangzh/first-try
 * branch            bug        -> FETCH_HEAD
   c5c305d..e9697ad  bug        -> origin/bug

$ git merge 'origin/bug'
Updating e123480..e9697ad
Fast-forward
 try0.txt | 4 +++-
 1 file changed, 3 insertions(+), 1 deletion(-)
```


