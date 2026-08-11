---
title: "使用qt5编写Serial Port"
description: "涉及信号-槽、多线程"
date: 2025-02-23T08:18:08+08:00
---

![RS-232 Testing Guide](https://www.eltima.com/images/upload/products/spm/articles/howto/178.jpg)

# thread
> 线程相关

## asynchronously
  [connect方法](https://doc.qt.io/qt-5/qobject.html#connect) 已实现异步。

## synchronously
[qmutexlocker](https://doc.qt.io/qt-5/qmutexlocker.html)
### 创建信号锁
```c++
m_mutex.lock();
```

## 信号
   为便于调用者获得线程的内部信息，我们需要提供信号。
```c++
signals:
void error(const QString &s);
void timeout(const QString &s);
```

![program ui](https://gitee.com/xiaobin80/cnblogs/raw/master/images/20200627152341.png)

# Worker method

## Init Port
   设置私有成员变量

> 部分代码
```c++
m_waitTimeout = waitTimeout;
```

## Receive data
  打开串口
```c++
if (!m_serialPort->open(QIODevice::ReadWrite)) {
		qDebug() << tr("Can't open %1, error code %2")
			.arg(m_portName).arg(m_serialPort->error());
		m_serialPort->close();
		return false;
	}
```

  接收数据
```c++
while (m_serialPort->waitForReadyRead(500))
            responseData += m_serialPort->read(1024);
```


# Deploy for windows
copy exe to target directory.
```bash
copy C:\Users\tdtc\Documents\Projects\serialCommQt\Release\serialCommQt.exe C:\Users\tdtc\Documents\serialCommQt_qmake\
```

exec windeployqt:
- for Widgets
```bash
C:\Qt\Qt5.12.11\5.12.11\msvc2017\bin\windeployqt --dir C:\Users\tdtc\Documents\Projects\serialCommQt C:\Users\tdtc\Documents\serialCommQt_qmake\serialCommQt.exe
```

![qt deploy files](https://raw.githubusercontent.com/tdtc-hrb/cnblogs/master/images/qt_deploy.png)

## About Qt Plugins
> The following files are copied from "C:\Qt\Qt5.12.11\5.12.11\msvc2017\plugins\platforms".
>> The "d" at the end of the file name means the debug version.
```bash
./platforms/qwindowsd.dll
```

All Qt GUI applications require a plugin that implements the Qt Platform Abstraction (QPA) layer in Qt 5.     
For Windows, the name of the platform plugin is qwindows.dll. 


# Reference
- [src](https://github.com/tdtc-hrb/serialCommQt)
- [Qt for Windows - Deployment](https://doc.qt.io/Qt-5/windows-deployment.html)
- [RS-232 Testing Guide](https://www.eltima.com/article/how-to-test-serial-port/)
