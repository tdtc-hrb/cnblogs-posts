---
title: "使用qt6的qml编写Serial Port"
description: "信号-槽、多线程、QML"
date: 2025-02-26T08:18:08+08:00
---
- Qt Environment
- Qt Singleton
- Qt Update Issue

## Qt Environment
- [Qt online installer](https://download.qt.io/official_releases/online_installers)
- [Visual Studio 2019/2022](https://tdtc-hrb.github.io/cnblogs/post/ops_win_faq2)

### installer
default:
- qt creator    
![qt-online-creator](https://raw.githubusercontent.com/tdtc-hrb/cnblogs/main/images/qt-online-creator.png)
- Build tools    
![qt-online-cmake](https://raw.githubusercontent.com/tdtc-hrb/cnblogs/main/images/qt-online-cmake.png)

#### MSVC
- vs2019(qt5.15)    
![qt5.15 msvc2019](https://raw.githubusercontent.com/tdtc-hrb/cnblogs/main/images/qt-online-msvc2019.png)
- vs2019    
![qt6.5 msvc2019](https://raw.githubusercontent.com/tdtc-hrb/cnblogs/main/images/qt6-online-msvc2019.png)
- vs2022    
![qt6.8 msvc2022](https://raw.githubusercontent.com/tdtc-hrb/cnblogs/main/images/qt-online-msvc2022.png)

#### Additional Libraries
- Qt5
> Included in the integration module

No need to select additional

- Qt6    
![Qt Serial Port](https://raw.githubusercontent.com/tdtc-hrb/cnblogs/main/images/qt6-online-serial_port.png)

### CMake in Qt Creator
程序设置：
```bash
-GNinja
-DCMAKE_BUILD_TYPE:STRING=Release
-DCMAKE_PROJECT_INCLUDE_BEFORE:PATH=%{IDE:ResourcePath}/package-manager/auto-setup.cmake
-DQT_QMAKE_EXECUTABLE:STRING=%{Qt:qmakeExecutable}
-DCMAKE_PREFIX_PATH:STRING=%{Qt:QT_INSTALL_PREFIX}
-DCMAKE_C_COMPILER:STRING=%{Compiler:Executable:C}
-DCMAKE_CXX_COMPILER:STRING=%{Compiler:Executable:Cxx}
```
### Deploy
- v5.15
```bash
C:\Qt\5.15.2\msvc2019_64\bin\windeployqt --qmldir C:\Users\tdtc\Documents\SerialCom\SerialCom\QtRaspberry C:\Users\tdtc\Documents\SerialCom_qml\serialCom.exe
```
- v6.5
```bash
C:\Qt\6.5.3\msvc2019_64\bin\windeployqt --qmldir C:\Users\tdtc\Documents\SerialCom\SerialCom\QtRaspberry C:\Users\tdtc\Documents\SerialCom_qml\serialCom.exe
```
- v6.8
```bash
C:\Qt\6.8.2\msvc2022_64\bin\windeployqt --qmldir C:\Users\tdtc\Documents\SerialCom\SerialCom\QtRaspberry C:\Users\tdtc\Documents\SerialCom_qml\serialCom.exe
```

## Qt Singleton
  The version that fully supports singletons is [v5.14+](https://doc.qt.io/qt-5/qqmlengine.html#qmlRegisterSingletonType-2)
```c++
template <typename T> int qmlRegisterSingletonType(const char *uri, int versionMajor, int versionMinor, const char *typeName, 
std::function<QObject *(QQmlEngine *, QJSEngine *)> callback)
```
  主要的改进是第5个参数！ 之前，是实例不了带参的类的。    
  The main improvement is the fifth parameter! Previously, only classes with no parameters could be instantiated.

- Example for v5.12: [qml-cpp-integration-example](https://github.com/RaymiiOrg/qml-cpp-integration-example/pull/1/commits/07d82aeea2ed9971fb6a4c61d0ce8a691824ca6e)
```c++
qmlRegisterSingletonType<TrafficLightClass>("org.raymii.RoadObjects", 1, 0, "TrafficLightSingleton",
                                     [](QQmlEngine *engine, QJSEngine *scriptEngine) -> QObject * {
        Q_UNUSED(engine)
        Q_UNUSED(scriptEngine)

        TrafficLightClass *trafficLightSingleton = new TrafficLightClass();
        return trafficLightSingleton;
    });
```
- Example for v5.14+: [qml-cpp-integration-example](https://raymii.org/s/articles/Qt_QML_Integrate_Cpp_with_QML_and_why_ContextProperties_are_bad.html)
```c++
 TrafficLightClass trafficLightSingleton;
    qmlRegisterSingletonType<TrafficLightClass>("org.raymii.RoadObjects", 1, 0, "TrafficLightSingleton",
                                     [&](QQmlEngine *, QJSEngine *) -> QObject * {
        return &trafficLightSingleton;
    });
```
## Qt Update Issue
> Issues when upgrading to Qt6

- [assigned values](https://forum.qt.io/post/589225)
- [signal parameters](https://stackoverflow.com/a/69279453)

### assigned values
```
ASSERT: "i >= 0 && i < size()" in file qbytearray.h, line 544
```
The "outBuffer" variable of the Frame class and the "m_buffer" variable of the SerialWorker class are assigned values, 
and the "=" is changed to "append()".
- For example, in frame.cpp    
Use 
```
 m_buffer.append(FRAME_START);
```
instead of 
```
m_buffer[INDEX_START_OF_FRAME] = FRAME_START;
```

### signal parameters
```
qt.qml.context: qrc:/main.qml:168:13 Parameter "on" is not declared. 
Injection of parameters into signal handlers is deprecated. Use JavaScript functions with formal parameters instead.
```
- v6
```
            onSwitched: function(on)
            {
                if(on === true)
                    FrameProcessorSingleton.enableAdc(1);
                else
                    FrameProcessorSingleton.enableAdc(0);
            }
```

## Ref
- [Qt and Serial Communication Tutorial](https://forums.raspberrypi.com/viewtopic.php?t=304199)
- [source - Demo](https://github.com/tdtc-hrb/SerialCom.git)
