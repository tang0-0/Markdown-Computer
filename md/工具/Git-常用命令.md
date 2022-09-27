- #### 系列文章
  [Git-入门小结](https://blog.csdn.net/lblmlms/article/details/105600621)
  [Git-分支](https://blog.csdn.net/lblmlms/article/details/107251420)
  [Git-常用命令](https://blog.csdn.net/lblmlms/article/details/107834005)
  [Git-注册远程仓库](https://blog.csdn.net/lblmlms/article/details/107554350)
## 1. 提交代码到暂存区

 ```c++
 git add .//添加当前目录的所有文件到缓存区
 git add "file1" "file2" //添加指定文件到缓存区
 git rm "file1" "file2"//删除工作区文件，并将这次删除放入缓存区
 git mv "originalname" "rename"//改名，并将这次改名存入缓存区
 ```
- ## 2.提交代码到版本库
  
   ```c++
   git commit -m "关于此次提交的描述"//提交整个暂存区到仓库
   git commit "file1 " file2" " -m "描述"//提交暂存区指定文件到仓库
   //git commit 之后若进步Vim编辑器，i进入输入状态，esc退出输入状态，w保存，q退出
   ```
## 3.查看相关信息

 ```c++
 git status//查看当前仓库状态
 git ls-files//显示暂存区中的文件 
 git log //显示当前分支的历史版本
 git log --follow "file"//显示指定文件的版本历史
 git diff//显示暂存区与工作区的差异
 git reflog//查询当前分支之前的所有操作
 git reflog "file" //查询指定文件的所有操作
 ```
## 4.回退版本

 ```c++
 git restore "file"//从暂存区退回工作区,2.23版本后才有
 git reset "file"//重置暂存区的指定文件，与仓库里的上一个版本保持一致，但工作区保持不变
 /*
 (1)--mixed：默认值，当重置分支所指向commit提交位置时，暂存区中的内容会被新指向的commit提交内容所替换，工作区内容不变。
 (2)--soft：暂存区和工作区的内容都保持原样，不会被替换。
 (3)--hard：暂存区和工作区的内容都会被新指向的commit提交内容所替换；git reset --hard只影响被跟踪的文件，如果工作区有新增的文件，并不会被影响。
 */
 git reset --hard <id>//工作区回退到指定版本，版本id可以用git log查询
 git rest --hard HEAD^//回退到当前分支的上个版本
 ```


![在这里插入图片描述](https://img-blog.csdnimg.cn/20200806104406482.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2xibG1sbXM=,size_16,color_FFFFFF,t_70)