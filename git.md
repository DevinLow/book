# **Git笔记**
### git 安装
   
```
sudo apt-get install git
```

### 初次配置
   
```
$ git config --global user.name "Your Name"
$ git config --global user.email "email@example.com"
```

### 初始化仓库
```
$ git init
Initialized empty Git repository in /Users/michael/learngit/.git/
```

### clone 代码
```
git clone git@192.168.1.203:/home/git/yeedonapp/xxx.git
```

### 创建本地分支
```
git checkout -b  分支名
```
### 将本地仓库与远程关联
```
touch xxx
git init
git add xxx
git commit -m "xxxx"
git remote add origin https://github.com/tugenhua0707/testgit 关联一个远程库
git push -u origin master
```
### 同步服务器代码
```
git pull
```
### 上传代码
```
git push origin master
```
### 上传本地分支到远程
```
git push origin 分支名

```
### 合并分支
```
git checkout -b master
git merge 分支名
```
### 服务器新建git 工程
```
sudo git init --bare sample.git
sudo chown -R git:git sample.git
```
### 补丁制作
```
#!/bin/bash
targetfolder=../update
targetname=update.zip
if [ $# != 2 ] ; then
        echo "USAGE: $0 OLD_ID NEW_ID" 
        exit 1;
fi
mkdir -p $targetfolder
git diff $1 $2 >../update/update.path
git archive -o ../update/new.zip $2 $(git diff --name-only $1 $2)  #新版本的差异文件
git archive -o ../update/old.zip $1 $(git diff --name-only $2 $1)  #旧版本的差异文件
cd ../update
unzip -o -d ./new/ new.zip
unzip -o -d ./old/ old.zip
rm new.zip old.zip
```
### 使用方法
> git log
commit 75df2c656ba0ff4654515922f1d26c102577c9e6
Author: DevinLow <gang_liu1987@163.com>
Date:   Thu Sep 29 15:21:39 2016 +0800
>
    usb 去抖

>commit e46d98b2818121b3639bc8a6cab0258581c513aa
Author: DevinLow <gang_liu1987@163.com>
Date:   Thu Sep 29 15:01:09 2016 +0800
>
    init project


>$./gitdiff.sh  e46d98b28181...   75df2c656ba..... 
Archive:  new.zip
75df2c656ba0ff4654515922f1d26c102577c9e6
   creating: ./new/mediatek/
   creating: ./new/mediatek/kernel/
   creating: ./new/mediatek/kernel/drivers/
   creating: ./new/mediatek/kernel/drivers/usb20/
  inflating: ./new/mediatek/kernel/drivers/usb20/musb_core.c  
Archive:  old.zip
e46d98b2818121b3639bc8a6cab0258581c513aa
   creating: ./old/mediatek/
   creating: ./old/mediatek/kernel/
   creating: ./old/mediatek/kernel/drivers/
   creating: ./old/mediatek/kernel/drivers/usb20/
  inflating: ./old/mediatek/kernel/drivers/usb20/musb_core.c  
liugang@ospicon-ubuntu:~/8127_mlc_kk$ 
在上一层的目录中就会生成 update目录
$ ls  ../update/ -l
ls: 初始化月份字符串出错
总用量 12
drwxrwxr-x 3 liugang liugang 4096  99 18:55 new
drwxrwxr-x 3 liugang liugang 4096  99 18:55 old
>-rw-rw-r-- 1 liugang liugang 1031  99 18:55 update.path

---
### 跟踪远程分支
```
git branch --set-upstream-to=origin/<branch>
git checkout --track origin/<branch>
git branch -u origin/master
```
### 拉取远程分支并创建本地分支
```
git checkout -b <local branch> origin/<remote branch>
git fetch origin <remote branch>:<local branch>
```
### push 代码到远程分支
```
git push origin <local branch>:<remote branch>
```
[别人整理的一些git 使用方法笔记](https://blog.csdn.net/xukai0110/article/details/80637902)
### 命令
```
因此：多人协作工作模式一般是这样的：

首先，可以试图用git push origin branch-name推送自己的修改.
如果推送失败，则因为远程分支比你的本地更新早，需要先用git pull试图合并。
如果合并有冲突，则需要解决冲突，并在本地提交。再用git push origin branch-name推送。
　　Git基本常用命令如下：

　　mkdir：         XX (创建一个空目录 XX指目录名)

　　pwd：          显示当前目录的路径。

　　git init          把当前的目录变成可以管理的git仓库，生成隐藏.git文件。

　　git add XX       把xx文件添加到暂存区去。

　　git commit –m “XX”  提交文件 –m 后面的是注释。

　　git status        查看仓库状态

　　git diff  XX      查看XX文件修改了那些内容

　　git log          查看历史记录

　　git reset  --hard HEAD^ 或者 git reset  --hard HEAD~ 回退到上一个版本

　　(如果想回退到100个版本，使用git reset –hard HEAD~100 )

　　cat XX         查看XX文件内容

　　git reflog       查看历史记录的版本号id

　　git checkout -- XX  把XX文件在工作区的修改全部撤销。

　　git rm XX          删除XX文件

　　git remote add origin https://github.com/tugenhua0707/testgit 关联一个远程库

　　git push –u(第一次要用-u 以后不需要) origin master 把当前master分支推送到远程库

　　git clone https://github.com/tugenhua0707/testgit  从远程库中克隆

　　git checkout –b dev  创建dev分支 并切换到dev分支上

　　git branch  查看当前所有的分支

　　git checkout master 切换回master分支

　　git merge dev    在当前的分支上合并dev分支

　　git branch –d dev 删除dev分支

　　git branch name  创建分支

　　git stash 把当前的工作隐藏起来 等以后恢复现场后继续工作

　　git stash list 查看所有被隐藏的文件列表

　　git stash apply 恢复被隐藏的文件，但是内容不删除

　　git stash drop 删除文件

　　git stash pop 恢复文件的同时 也删除文件

　　git remote 查看远程库的信息

　　git remote –v 查看远程库的详细信息

　　git push origin master  Git会把master分支推送到远程库对应的远程分支上
```