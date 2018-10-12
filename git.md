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
