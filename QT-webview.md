---
title: "qtwebview minibrowser example"
description: "qt quick"
date: 2025-10-03T23:38:08+08:00
---
- [Android Studio](https://developer.android.google.cn/studio)
- [mirror - online installer](https://download.qt.io/static/mirrorlist/mirmon/)
```
qt-online-installer-windows-x64-x.x.x.exe --mirror Y
```


## Create Qt Project
File -> New Project
- v6.x    
Qt Quick Application
- v5.15    
Qt Quick Application(compat)

### project setting
- project name
```
my_browser
```
- Define Build System
```
CMake
```
- Define Project Details    
Mini Qt version:
```
Qt 5.15/6.5
```
- Kit Selection
```
Android Qt x.x.x Clang x86_64
```

### [Using Qt WebView](https://doc.qt.io/qt-6.8/qtwebview-index.html)
- QML API
```qml
import QtWebView
```
- C++ API
```
#include <QtWebView/QtWebView>
```

#### Building with CMake
- package
```
find_package(Qt6 REQUIRED COMPONENTS Quick WebView)
```
- target
```
target_link_libraries(appmy_browser
    PRIVATE Qt6::Quick Qt6::WebView
)
```

#### initial
```c++
// first line
QtWebView::initialize();
```

### Icon
copy "Examples\Qt-6.x.x\webview\minibrowser\images" to project folder

#### [Qt 6.x](https://forum.qt.io/topic/144345/qml-image-cannot-open-cmake-qrc/10)
> CMakeLists.txt
```
qt_add_qml_module(appmy_browser
    URI my_browser
    VERSION 1.0
    QML_FILES 
        Main.qml
    RESOURCES 
        images/left-32.png
        images/refresh-32.png
        images/right-32.png
        images/stop-32.png
)
```

#### Qt 5.15
> qml.qrc
```
<RCC>
    <qresource prefix="/">
        <file>main.qml</file>
        <file>images/left-32.png</file>
        <file>images/stop-32.png</file>
        <file>images/refresh-32.png</file>
        <file>images/right-32.png</file>
    </qresource>
</RCC>
```


## implementation
- Layouts
```qml
import QtQuick.Layouts
```
- Button
The Qt examples use ToolButton, but we use Button.

usage [Button](https://forum.qt.io/topic/134678/toolbutton-icon-source/10) 替换 toolbutton

- Icon Image
Use IconImage instead of iconSource.
- v6.8    
The [auto behavior](https://codereview.qt-project.org/c/qt/qtdeclarative/+/547022/4/src/quickcontrols/basic/CMakeLists.txt#b162) 
was changed in [QTBUG-123103](https://bugreports.qt.io/browse/QTBUG-123103), 
so IconImage must be explicitly introduced!
```
import QtQuick.Controls.impl
```

### Styles
Q: [Cannot assign to non-existent property "style"](https://stackoverflow.com/questions/49263604/qml-cannot-assign-to-non-existent-property-style)

A: 2.0已经不支持 QtQuick.Controls.Styles

### WebView
```qml
    WebView {
        id: webView
        anchors.fill: parent
        url: "https://www.qt.io"
        onLoadingChanged: function(loadRequest) {
            if (loadRequest.errorString)
                console.error(loadRequest.errorString);
        }
    }
```

#### WebViewLoadRequest
In v6.8 the parameter is used explicitly; 
In v6.5 it can be used implicitly.
- v6.8
```
onLoadingChanged: function(loadRequest) { //... }
```
- v6.5
```
onLoadingChanged: { //... }
```

### [tool bar](https://doc.qt.io/qt-6/qml-qtquick-controls2-toolbar.html)
ToolBar is a container of application-wide and context sensitive actions and controls, 
such as navigation buttons and search fields. 
ToolBar is commonly used as a header or a footer of an ApplicationWindow.

- footer    
要使用footer属性时，必须使用
```
import QtQuick.Controls
ApplicationWindow { //... }
```

#### Main.qml(part)
```qml
footer: ToolBar {
    id: navigationBar
    height: 64

    RowLayout {
        anchors.fill: parent
        spacing: 0

        Button {
            contentItem: Row {
                IconImage {
                    source: "images/left-32.png"
                }
                Text {
                     text: qsTr("<")
                }
            }
            enabled: webView.canGoBack
            onClicked: webView.goBack()
        }

        Item { Layout.preferredWidth: 5 }

        Button {
            contentItem: Row {
                IconImage {
                    source: "images/right-32.png"
                }
                Text {
                     text: qsTr(">")
                }
            }
            enabled: webView.canGoForward
            onClicked: webView.goForward()
        }

        Item { Layout.preferredWidth: 5 }

        Button {
            contentItem: Row {
                IconImage {
                    source: webView.loading ? "images/stop-32.png" : "images/refresh-32.png"
                }
                Text {
                    text: qsTr("Ref/Stop")
                }
            }
           background: Rectangle {
               color: "steelblue"
           }

           onClicked: webView.loading ? webView.stop() : webView.reload()
        }

        Item { Layout.preferredWidth: 10 }
    }
}
```


## Ref
- [Minibrowser](https://doc.qt.io/qt-6/qtwebview-minibrowser-example.html)
- [WebEngineView ≠ WebView](https://decovar.dev/blog/2018/07/14/html-from-qml-over-webchannel-websockets/)
