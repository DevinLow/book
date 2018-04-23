#Dbus 调试命令
```
dbus-monitor --system \ "type='signal',interface='com.qt.yeedon.app'" 
dbus-send --system --print-reply --dest=com.qt.yeedon.app  /  com.qt.yeedon.app.sendMsgToApp int32:2020 int32:1
gst-launch-1.0 filesrc location="/run/media/usb/T04_127HZ_SINE_WAVE.MP3" ! mpegaudioparse ! avdec_mp3 ! audioresample ! alsasink device=hw:2,0
```