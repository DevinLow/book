### QtDBUS 
```
使用方法，先编写.h 文件

qdbuscpp2xml -M -s dbus.h -o com.qt.example.dbus.xml  生成序列文件

qdbusxml2cpp com.qt.example.dbus.xml -a dbus\_adaptor 生成适配器文件

qdbusxml2cpp com.qt.example.dbus.xml -p dbus\_interface 生成接口文件

```

```
阴影
Rectangle {
    width: 500
    height: 500
    Rectangle{
        width: 300
        height: 300
        id:rect
        anchors.centerIn: parent
        color:"red"
    }

    DropShadow {
        anchors.fill: rect
        horizontalOffset: 3
        verticalOffset: 3
        radius: 8.0
        samples: 16
        color: "#80000000"
        source: rect
    }
}
```