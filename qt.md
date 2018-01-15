### QtDBUS 
```
使用方法，先编写.h 文件

qdbuscpp2xml -M -s dbus.h -o com.qt.example.dbus.xml  生成序列文件

qdbusxml2cpp com.qt.example.dbus.xml -a dbus\_adaptor 生成适配器文件

qdbusxml2cpp com.qt.example.dbus.xml -p dbus\_interface 生成接口文件

```