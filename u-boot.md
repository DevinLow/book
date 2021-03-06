# **u-boot笔记**
### 功能需求：通过usb stroage 实现固件升级
### 解决思路：利用u-boot的USB CMD和 FAT CMD 实现写入动作
---
#### 1，在uboot 命令行，敲入help 命令，查询相关USB CMD  FAT CMD  是否启用。
#### 2，查阅u-boot 的README，找到相关内容，找到配置所需功能的方式。

### 相关信息：
>  - Monitor Functions:
Monitor commands can be included or excluded from the build by using the #include files <config_cmd_all.h> and #undef'ing unwanted commands, or adding #define's for wanted commands.
- The default command configuration includes all commands except those marked below with a "*".

>- CONFIG_CMD_USB &emsp;&emsp;&emsp;&emsp;USB support
>- USB Support:At the moment only the UHCI host controller is supported (PIP405, MIP405, MPC5200); define CONFIG_USB_UHCI to enable it. define CONFIG_USB_KEYBOARD to enable the USB Keyboard and define CONFIG_USB_STORAGE to enable the USB storage devices.
>- CONFIG_USB_STORAGE &emsp;&emsp;&emsp;&emsp;USB support

---
#### 4,找到对应方案的defconfig
>- u-boot/configs

```
$ ls sta-* -l
-rw-r--r-- 1 devin 197121  983 11月  2 19:44 sta-mmc-flashloader_defconfig
-rw-r--r-- 1 devin 197121  958 11月  2 19:44 sta-mmc-xloader_defconfig ----this is we need
-rw-r--r-- 1 devin 197121  922 11月  2 19:44 sta-nand-flashloader_defconfig
-rw-r--r-- 1 devin 197121  880 11月  2 19:44 sta-nand-xloader_defconfig
-rw-r--r-- 1 devin 197121 1067 11月  2 19:44 sta-sqi-flashloader_defconfig
-rw-r--r-- 1 devin 197121 1178 11月  2 19:44 sta-sqi-mmc-flashloader_defconfig
-rw-r--r-- 1 devin 197121 1111 11月  2 19:44 sta-sqi-nand-flashloader_defconfig
```
根据项目资料，我们能确定sta-mmc-xloader_defconfig就是我们的配置，所以我们需要在里面添加上     
CONFIG_CMD_USB=y &emsp;&emsp;&emsp; //enable USB CMD soupport
CONFIG_USB_STORAGE=y &emsp;&emsp;&emsp;//enable USB storage soupport   
完成这些，我们基本已经打开相关支持，此时我们需要编译代码，先在u-boot 命令行模拟一下升级过程。

>有可能用到的
 CONFIG_USB_OHCI
 CONFIG_CMD_FAT
 CONFIG_DOS_PARTITION
 CONFIG_SUPPORT_VFAT

---    
### 具体操作：
#### 1，在uboot中执行help usb查看支持的命令

```
//待添加
```

#### 2，插入U盘，先执行usb start命令，或使用usb reset也是一样的动作，接着使用usb dev可以查看检测到的USB设备

```
//待添加
```
#### 3，使用usb info可列出板上USB HOST接口的属性

```
//待添加
```
#### 4,使用usb storage可列出U盘设备信息，使用usb tree列出Device Tree

```
//待添加
```
#### 5，使用usb part列出U盘设备的分区信息

```
//待添加
```

#### 6，使用完U盘后，要使用usb stop停止设备

```
//待添加
```
### 操作U盘中的文件需要借助FAT文件系统，所有存储设备都可以用FAT文件系统统一操作：

#### 1，执行help中可以看到有3个fat命令，fatls fatinfo fatload

```
//待添加
```

#### 2，fatls: list files in a directory(default /)

``` 
用法：<interface> <dev[:part]> [directory]
```

#### interface就是指哪种类型的设备，我们用的是usb，dev就是设备号，我们的设备号是0，默认的directory是根目录
#### 执行：fatls usb 0
#### 列出U盘上的文件系统目录结构：

```
//待添加
```
### 3,fatinfo： print information about filesystem
```
用法：<interface> <dev[:part]>
```
#### 执行：fatinfo usb 0
```
//待添加
```
### 4,fatload：load binary file from a dos filesystem
```
用法：<interface> <dev[:part]>  <addr> <filename> [bytes]
```
#### 从U盘中读取文件到指定内存地址，比如我U盘上有一个名为uboot.bin的文件，读到0x30000000的地址处
#### 执行：fatload usb 0 0x30000000 uboot.bin
#### 读完后就可以用mmc write命令将30000000处理的数据写到bios分区中，这就实现了用U盘烧写系统功能

### 我们的系统的存储介质是MMC，最终将文件写入待EMMC需要用到MMC 命令

#### 1，首先在uboot下执行mmc，可得：
```
mmc info - display info of the current MMC device  
mmc read addr blk# cnt
mmc write addr blk# cnt
mmc erase blk# cnt
mmc rescan
mmc part - lists available partition on current mmc device
mmc dev [dev] [part] - show or set current mmc device [partition]
mmc list - lists available devices
mmc setdsr <value> - set DSR register value
```
#### 2,mmc info - display info of the current MMC device   显示存储设备的信息
#### 在切换到emmc设备下（执行mmc dev 1），执行mmcinfo可看到存储设备各属性信息：
```
//待添加
```
#### 3,mmc write addr blk# cnt  将内存的地址为addr内容写到存储设备中，其中起始地址是blk，大小为cnt （单位都为块）
#### 4,mmc    write 0x1080000  1de000     10000  将内存1080000开始的内容，写到emmc的1de000开始大小为10000
#### SD卡更新uboot>bin示例：
```
mmc dev 0
mmcinfo
fatload usb 0 0x1080000 uboot.bin //（SD卡首目录下文件）
mmc write 1080000 0 2000
```

































