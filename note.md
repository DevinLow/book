### Dbus 调试命令
```
dbus-monitor --system \ "type='signal',interface='com.qt.yeedon.app'" 
dbus-send --system --print-reply --dest=com.qt.yeedon.app  /  com.qt.yeedon.app.sendMsgToApp int32:2020 int32:1
gst-launch-1.0 filesrc location="/run/media/usb/T04_127HZ_SINE_WAVE.MP3" ! mpegaudioparse ! avdec_mp3 ! audioresample ! alsasink device=hw:2,0
```
### repo 使用方式
```shell
export REPO_URL='https://mirrors.tuna.tsinghua.edu.cn/git/git-repo/'
repo init -u http://192.168.1.252/dp9510_dv11/manifests.git
repo sync
```shell
dbus-send --system --print-reply --dest=com.qt.yeedon.can / com.qt.yeedon.can.sengMsgToM3 uint32:42 uint32:1
dbus-send --system --print-reply --dest=com.qt.yeedon.dbus / com.qt.yeedon.dbus.setMasterVolume int32:30
dbus-send --system --print-reply --dest=com.qt.yeedon.dbus / com.qt.yeedon.dbus.setMuteAmpi boolean:false
 dbus-send --system --print-reply --dest=com.qt.yeedon.dbus / com.qt.yeedon.dbus.addVolumeMaster
dbus-send --system --print-reply --dest=com.yeedon.ex50.sourcemanager / com.yeedon.ex50.sourcemanager.echo
dbus-send --system --print-reply --dest=com.qt.yeedon.dbus / com.qt.yeedon.dbus.setSource int32:1 
dbus-send --system --print-reply --dest=com.qt.yeedon.mem / com.qt.yeedon.mem.get_CurData int32:3 
dbus-send --system --print-reply --dest=com.yeedon.ex50.sourcemanager / com.yeedon.ex50.sourcemanager.echo 
dbus-send --system --print-reply --dest=com.bt.seivice.dbus / com.bt.seivice.dbus.setMute int32:1 
dbus-send --system --print-reply --dest=com.bt.seivice.dbus / com.bt.seivice.dbus.setDevName string:'你好' 
dbus-send --system --print-reply --dest=com.bt.seivice.dbus / com.bt.seivice.dbus.pairTableDataTwoName string:'15625106205' 
dbus-send --system --print-reply --dest=com.bt.seivice.dbus / com.bt.seivice.dbus.controlCall int32:5 string:'1' 
dbus-send --system --print-reply --dest=com.bt.seivice.dbus / com.bt.seivice.dbus.controlMusic int32:0 
dbus-send --system --print-reply --dest=com.bt.seivice.dbus / com.bt.seivice.dbus.pair_devs string:'54:33:CB:DE:A4:A4' boolean:true 
dbus-send --system --print-reply --dest=com.bt.seivice.dbus / com.bt.seivice.dbus.downloadContact int32:2 
dbus-send --system --print-reply --dest=com.qt.yeedon.tuner / com.qt.yeedon.tuner.echo 
dbus-send --system --print-reply --dest=com.qt.yeedon.tuner / com.qt.yeedon.tuner.seekUp 
dbus-send --system --print-reply --dest=com.qt.yeedon.tuner / com.qt.yeedon.tuner.learnStart string:'FM' 
dbus-send --system --print-reply --dest=com.qt.yeedon.app / com.qt.yeedon.app.sendMsgToApp int32:2005 int32:0 
dbus-send --system --print-reply --dest=com.qt.yeedon.app / com.qt.yeedon.app.sendMsgToApp int32:2007 int32:1
 dbus-send --system --print-reply --dest=com.qt.yeedon.app / com.qt.yeedon.app.sendMsgToApp int32:2020 int32:1 
dbus-send --system --print-reply --dest=com.qt.yeedon.dbus / com.qt.yeedon.dbus.setMasterVolume int32:25 
dbus-send --system --print-reply --dest=com.qt.yeedon.dbus / com.qt.yeedon.dbus.requstSource string:'btmusic' int32:2
dbus-send --system --print-reply --dest=com.yeedon.ex50.sourcemanager / com.yeedon.ex50.sourcemanager.SetObtainLicense int32:5
```
```
echo 0 > /sys/devices/platform/soc/soc:backlight/backlight_gpio/backlight_gpio
echo 1 > /sys/devices/platform/soc/soc:backlight/backlight_gpio/backlight_gpio
dbus-monitor --system \ "type='signal',interface='com.tiot.dbus.hardkey'"
dbus-monitor --system \ "type='signal',interface='com.bt.seivice.dbus'"
dbus-monitor --system \ "type='signal',interface='com.qt.yeedon.can',menber='sigCanMsg'"
dbus-send --system --print-reply --dest=com.yeedon.ex50.sourcemanager / com.yeedon.ex50.sourcemanager.echo 
dbus-monitor --system \ "type='signal',interface='com.yeedon.ex50.sourcemanager'"&
```
```
ip tuntap add eryanet mode tap
ifconfig eryanet 192.168.3.3 netmask 255.255.255.0 broadcast 192.168.3.255 up
route add -net 192.168.3.0 netmask 255.255.255.0 dev eryanet
route add default gw 192.168.3.3
ifconfig查看eryanet的状态是否正确
iptables -t nat -A OUTPUT -p tcp -j REDIRECT --to-ports 12345
iptables-save查看

root@sta1295-evb-mmc-1g:~# ip tuntap add eryanet mode tap
root@sta1295-evb-mmc-1g:~# 
root@sta1295-evb-mmc-1g:~# ifconfig eryanet 192.168.3.3 netmask 255.255.255.0 broadcast 192.168.3.255 up
root@sta1295-evb-mmc-1g:~# route add -net 192.168.3.0 netmask 255.255.255.0 dev eryanet
root@sta1295-evb-mmc-1g:~# route add default gw 192.168.3.3
root@sta1295-evb-mmc-1g:~# ifconfig
eryanet   Link encap:Ethernet  HWaddr f6:fb:50:e6:7e:ea  
          inet addr:192.168.3.3  Bcast:192.168.3.255  Mask:255.255.255.0
          UP BROADCAST MULTICAST  MTU:1500  Metric:1
          RX packets:0 errors:0 dropped:0 overruns:0 frame:0
          TX packets:0 errors:0 dropped:0 overruns:0 carrier:0
          collisions:0 txqueuelen:1000 
          RX bytes:0 (0.0 B)  TX bytes:0 (0.0 B)

lo        Link encap:Local Loopback  
          inet addr:127.0.0.1  Mask:255.0.0.0
          inet6 addr: ::1/128 Scope:Host
          UP LOOPBACK RUNNING  MTU:65536  Metric:1
          RX packets:2 errors:0 dropped:0 overruns:0 frame:0
          TX packets:2 errors:0 dropped:0 overruns:0 carrier:0
          collisions:0 txqueuelen:1 
          RX bytes:78 (78.0 B)  TX bytes:78 (78.0 B)

root@sta1295-evb-mmc-1g:~# 
root@sta1295-evb-mmc-1g:~# 
root@sta1295-evb-mmc-1g:~# iptables -t nat -A OUTPUT -p tcp -j REDIRECT --to-ports 12345
root@sta1295-evb-mmc-1g:~# iptables-save
# Generated by iptables-save v1.6.1 on Fri Oct 12 11:31:01 2018
*raw
:PREROUTING ACCEPT [10:2200]
:OUTPUT ACCEPT [10:2200]
COMMIT
# Completed on Fri Oct 12 11:31:01 2018
# Generated by iptables-save v1.6.1 on Fri Oct 12 11:31:01 2018
*nat
:PREROUTING ACCEPT [0:0]
:INPUT ACCEPT [0:0]
:OUTPUT ACCEPT [0:0]
:POSTROUTING ACCEPT [0:0]
-A OUTPUT -p tcp -j REDIRECT --to-ports 12345
COMMIT
# Completed on Fri Oct 12 11:31:01 2018
# Generated by iptables-save v1.6.1 on Fri Oct 12 11:31:01 2018
*mangle
:PREROUTING ACCEPT [10:2200]
:INPUT ACCEPT [10:2200]
:FORWARD ACCEPT [0:0]
:OUTPUT ACCEPT [10:2200]
:POSTROUTING ACCEPT [19:4371]
COMMIT
# Completed on Fri Oct 12 11:31:01 2018
# Generated by iptables-save v1.6.1 on Fri Oct 12 11:31:01 2018
*filter
:INPUT ACCEPT [10:2200]
:FORWARD ACCEPT [0:0]
:OUTPUT ACCEPT [10:2200]
COMMIT
# Completed on Fri Oct 12 11:31:01 2018
```
```
git log --date=iso --pretty=format:’”%h”,”%an”,”%ad”,”%s”’ > ./commit.csv
```
```
清除掉eMMC boot partition里面的内容就可以了
####### How to erase the content in eMMC boot partition 1 on cut3.0 ##########
1. enter uboot cmd console
2. mmc list
3. select eMMC, "mmc dev 1"                              OR "mmc dev 2"
4. mmc partconf 1 0 1 1  ## select boot partition 1      OR "mmc partconf 2 0 1 1"
5. mmc erase 0 0x400 ## erase
```
```
gst-play-1.0 ./123.mp4 --videosink "preferred-caps=="video/x-raw,width=1024,height=600" ! waylandsink" autovideosink
gst-launch-1.0 filesrc location=./Xvid\ video\ 2.avi ! avidemux name=d ! queue ! mpegaudioparse ! avdec_mp3 ! audioconvert ! audioresample ! alsasink device=hw:1,0 d. ! queue ! mpeg4videoparse ! queue ! v4l2dec preferred-caps="video/x-raw, width=1280, height=480" ! queue ! waylandsink
```
```
LayerManagerControl create layer 1000 1280 480 
LayerManagerControl set layer 1000 render order 1000 
LayerManagerControl set surface 14 source region 0 0 1280 480 
LayerManagerControl set surface 14 destination region 0 0 1280 480 
LayerManagerControl set screen 0 render order 1000 `
```
