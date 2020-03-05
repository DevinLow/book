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

core dump 文件生成与使用方式
```c++
#include "CoredumpHelper.h"

#include <unistd.h>
#include <sys/time.h>
#include <sys/resource.h>
#include <stdio.h>
#include <stdlib.h>

#define CORE_SIZE   1024 * 1024 * 500

CoredumpHelper::CoredumpHelper() {
    struct rlimit rlmt;
    getrlimit(RLIMIT_CORE, &rlmt);
    printf("Before set rlimit CORE dump current is:%d, max is:%d\n", (int)rlmt.rlim_cur, (int)rlmt.rlim_max);
    rlmt.rlim_cur = (rlim_t)CORE_SIZE;
    rlmt.rlim_max  = (rlim_t)CORE_SIZE;
    setrlimit(RLIMIT_CORE, &rlmt);
    getrlimit(RLIMIT_CORE, &rlmt);
    printf("After set rlimit CORE dump current is:%d, max is:%d\n", (int)rlmt.rlim_cur, (int)rlmt.rlim_max);
    system("echo 1 > /proc/sys/kernel/core_uses_pid");
    system("echo \"/home/root/corefile-%e-%p-%t\" > /proc/sys/kernel/core_pattern");
}
```
```shell
gdb program corefile-program-143-2056
```
## click Enter key -> key input "where" -> click Enter key





