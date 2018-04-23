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

###clone 代码
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
###服务器新建git 工程
```
sudo git init --bare sample.git
```
