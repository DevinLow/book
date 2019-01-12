#Dbus 调试命令
```
dbus-monitor --system \ "type='signal',interface='com.qt.yeedon.app'" 
dbus-send --system --print-reply --dest=com.qt.yeedon.app  /  com.qt.yeedon.app.sendMsgToApp int32:2020 int32:1
gst-launch-1.0 filesrc location="/run/media/usb/T04_127HZ_SINE_WAVE.MP3" ! mpegaudioparse ! avdec_mp3 ! audioresample ! alsasink device=hw:2,0
```
#repo 使用方式
```
export REPO_URL='https://mirrors.tuna.tsinghua.edu.cn/git/git-repo/'
repo init -u http://192.168.1.252/dp9510_dv11/manifests.git
repo sync
```
