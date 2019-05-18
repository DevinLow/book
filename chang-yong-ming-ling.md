--- 
#### 1.DTS反编译


```shell
dtc -O dts -o tmp.dts -I dtb  path for dtb

```

#### 2.Android selinux 开关

```shell
adb shell
#获取selinux 状态
getenforce
#设置selinux 状态
setenforce 1 #开启
setenforce 0 #关闭
```
#### 3，adb shell root 权限获取


```shell
adb root 
adb remount
```







