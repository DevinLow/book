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
```
阴影button
import QtQuick 2.6  
import QtGraphicalEffects 1.0  
  
/****************************************************************************  
**自定义按钮功能实现  
**主要实现按钮的按压效果，带有阴影效果  
**  
****************************************************************************/  
  
Item {  
    id : mybutton  
    width: 100  
    height: 50  
  
    signal clicked();  
  
    Rectangle {  
        id : bgrect;  
        y : 20  
        x : 1  
        color: "#5CB85C";  
        width: mybutton.width-2;  
        height: mybutton.height-25  
        radius: height/2  
    }  
  
    DropShadow {  
        id : shadow  
        anchors.fill: bgrect  
        horizontalOffset: 2  
        verticalOffset: 12  
        radius: 8.0  
        samples: 17  
        color: "#999999"  
        source: bgrect  
    }  
  
  
    Rectangle{  
        id : toprect  
        color: "#5CB85C"  
        width: mybutton.width;  
        height: mybutton.height-2  
        radius: height/3  
  
        MouseArea {  
            id: mouseArea  
            anchors.fill: parent  
            hoverEnabled : true  
            onClicked: {  
               animation.start();  
               mybutton.clicked();  
            }  
            onEntered: {  
                toprect.color = "#3e8e41";  
                bgrect.color = "#3e8e41";  
            }  
            onExited: {  
                toprect.color = "#5CB85C";  
                bgrect.color = "#5CB85C";  
            }  
  
        }  
  
    }  
  
    Text {  
        id: mytext  
        anchors.centerIn: toprect  
        text: qsTr("text")  
        color: "#ffffff"  
        font.pixelSize : toprect.height/2  
    }  
  
  
    SequentialAnimation {  
  
              id : animation  
              NumberAnimation { target: toprect; property: "y"; to: toprect.x+2; duration: 100 }  
              NumberAnimation { target: toprect; property: "y"; to: toprect.x-2; duration: 100 }  
          }  
  
}  
```
```
flat style button
import QtQuick 2.8
import QtQuick.Templates 2.1 as T
import Theme 1.0

T.Button {
    id: control

    font: Theme.font

    implicitWidth: Math.max(background ? background.implicitWidth : 0,
                                         contentItem.implicitWidth + leftPadding + rightPadding)
    implicitHeight: Math.max(background ? background.implicitHeight : 0,
                                          contentItem.implicitHeight + topPadding + bottomPadding)
    leftPadding: 4
    rightPadding: 4

    background: Rectangle {
        id: buttonBackground
        implicitWidth: 100
        implicitHeight: 40
        opacity: enabled ? 1 : 0.3
        border.color: Theme.mainColor
        border.width: 1
        radius: 2

        states: [
            State {
                name: "normal"
                when: !control.down
                PropertyChanges {
                    target: buttonBackground
                }
            },
            State {
                name: "down"
                when: control.down
                PropertyChanges {
                    target: buttonBackground
                    border.color: Theme.mainColorDarker
                }
            }
        ]
    }

    contentItem: Text {
        id: textItem
        text: control.text

        font: control.font
        opacity: enabled ? 1.0 : 0.3
        color: Theme.mainColor
        horizontalAlignment: Text.AlignHCenter
        verticalAlignment: Text.AlignVCenter
        elide: Text.ElideRight

        states: [
            State {
                name: "normal"
                when: !control.down
            },
            State {
                name: "down"
                when: control.down
                PropertyChanges {
                    target: textItem
                    color: Theme.mainColorDarker
                }
            }
        ]
    }
}
```