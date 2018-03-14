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
```
cover flow

Rectangle {
    id:coverflow
    color:"black"

    property ListModel model
    property int itemCount: 5

    PathView{
        id:pathView
        model:coverflow.model
        delegate: Item {
            id:delegateItem
            width: 200
            height: 200
            z:PathView.iconZ
            scale:PathView.iconScale

            Image{
                id:image
                source: url
                width: delegateItem.width
                height: delegateItem.height
            }
            ShaderEffect {
                anchors.top: image.bottom
                width: image.width
                height: image.height;
                anchors.left: image.left
                property variant source: image;
                property size sourceSize: Qt.size(0.5 / image.width, 0.5 / image.height);
                fragmentShader:
                        "varying highp vec2 qt_TexCoord0;
                        uniform lowp sampler2D source;
                        uniform lowp vec2 sourceSize;
                        uniform lowp float qt_Opacity;
                        void main() {

                            lowp vec2 tc = qt_TexCoord0 * vec2(1, -1) + vec2(0, 1);
                            lowp vec4 col = 0.25 * (texture2D(source, tc + sourceSize) + texture2D(source, tc- sourceSize)
                            + texture2D(source, tc + sourceSize * vec2(1, -1))
                            + texture2D(source, tc + sourceSize * vec2(-1, 1)));
                            gl_FragColor = col * qt_Opacity * (1.0 - qt_TexCoord0.y) * 0.2;
                        }"
            }



            transform: Rotation{
                origin.x:image.width/2.0
                origin.y:image.height/2.0
                axis{x:0;y:1;z:0}
                angle: delegateItem.PathView.iconAngle
            }
        }
        path:coverFlowPath
        pathItemCount: coverflow.itemCount
        anchors.fill: parent

        preferredHighlightBegin: 0.5
        preferredHighlightEnd: 0.5

    }

    Path{
        id:coverFlowPath
        startX: 0
        startY: coverflow.height/3

        PathAttribute{name:"iconZ";value: 0}
        PathAttribute{name:"iconAngle";value: 70}
        PathAttribute{name:"iconScale";value: 0.6}
        PathLine{x:coverflow.width/2;y:coverflow.height/3}

        PathAttribute{name:"iconZ";value: 100}
        PathAttribute{name:"iconAngle";value: 0}
        PathAttribute{name:"iconScale";value: 1.0}

        PathLine{x:coverflow.width;y:coverflow.height/3}
        PathAttribute{name:"iconZ";value: 0}
        PathAttribute{name:"iconAngle";value: -70}
        PathAttribute{name:"iconScale";value: 0.6}
        PathPercent{value:1.0}

    }

}
```
```
倒影
import QtQuick 2.0
import QtQuick.Controls 1.4

Rectangle {
    id: window

    width: 600
    height: 500

    gradient: Gradient {
        GradientStop { position: 0; color: "lightsteelblue" }
        GradientStop { position: 1; color: "black" }
    }

    Image {
        id: img
        width: 300
        height: 170
        source: "car.png"
        anchors.centerIn: parent
    }
    Item{
            width: img.width
            height: img.height

            Image{
                source: img.source
                anchors.fill: parent
                transform: Scale{
                    yScale: -1
                    origin.y:img.height/2.
                }
            }
            LinearGradient{
                width: img.width
                height: img.height
                gradient: Gradient{
                    GradientStop{position: 0.0;color:Qt.rgba(0,0,0,0.1)}
                    GradientStop{position: 0.4;color:Qt.rgba(0,0,0,1)}
                }
            }
        }
}
```