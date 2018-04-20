#Dbus 调试命令
```
dbus-monitor --system \ "type='signal',interface='com.qt.yeedon.app'" 
dbus-send --system --print-reply --dest=com.qt.yeedon.app  /  com.qt.yeedon.app.sendMsgToApp int32:2020 int32:1
```