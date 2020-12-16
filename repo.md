## repo

### 如何通过mirror 得到代码
```shell
repo init -u file://[manifest的git的绝对路径] -b [分支] -m [xml位置] --reference=/[mirror的绝对路径]

repo init --repo-url  http://github.com/aosp-mirror/tools_repo.git -u http://github.com/rockchip-toybrick/manifest.git -b master -m rk3399pro.xml --reference=/[mirror的绝对路径]


```
