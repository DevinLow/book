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
--------------------- 
>作者：Devin_la 
来源：CSDN 
原文：https://blog.csdn.net/a193314/article/details/52704393?utm_source=copy 
版权声明：本文为博主原创文章，转载请附上博文链接！

