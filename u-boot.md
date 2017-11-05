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

```


















































