---
title: "Widgets简单demo"
description: "Just a pop-up dialog box"
date: 2025-09-25T19:08:08+08:00
---
- [VC++](https://tdtc-hrb.github.io/ops-win/posts/vc-win)
- Qt Design Studio

### [Qt Visual Studio Tools](https://marketplace.visualstudio.com/items?itemName=TheQtCompany.QtVisualStudioTools2022)
Find "Qt" in "Tools->Options" of Visual Studio and set the qmake path
![Versions - vs2022](https://github.com/tdtc-hrb/cnblogs/raw/main/images/20250123104033.png)

# Widget工程
- QtHello
![vs2022 info1](https://github.com/tdtc-hrb/cnblogs/raw/main/images/20250123111036.png)

![Build config](https://github.com/tdtc-hrb/cnblogs/raw/main/images/20250123130638.png)

![set class name](https://github.com/tdtc-hrb/cnblogs/raw/main/images/20250123130026.png)
"User Interface(.ui)file" is only available if you install the QtDesignStudio

## UI
- Open the UI file using QT
![QtDesignStudio](https://github.com/tdtc-hrb/cnblogs/raw/main/images/20250123131837.png)

### Push Button
拖拽“Push Button”，并修改属性：
- ObjectName
```
btnHello
```
- Text
```
Click Me
```


# Code Implementation
- QtHello.h
```c++
#pragma once

#include <QtWidgets/QMainWindow>
#include "ui_QtHello.h"

class QtHello : public QMainWindow
{
    Q_OBJECT

public:
    QtHello(QWidget *parent = Q_NULLPTR);

private:
    Ui::QtHelloClass ui;

// add code // Declarative methods
private slots:
    void helloQT();
};
```
- QtHello.cpp
```c++
#include "QtHello.h"
#include <QtWidgets/QMessageBox>

QtHello::QtHello(QWidget *parent)
    : QMainWindow(parent)
{
    ui.setupUi(this);

    // add code // Association 3-Elements: Ui-Event-Method
    connect(ui.btnHello, &QPushButton::clicked, this, &QtHello::helloQT);
    // The other way to connect a signal to a slot is to use QObject::connect() and the SIGNAL and SLOT macros. 
    // connect(ui.btnHello, SIGNAL(clicked()), this, SLOT(helloQT()));
}

// add code // Implementation
void QtHello::helloQT()
{
    QMessageBox msgBox;
    msgBox.setText("Hello Qt!");
    msgBox.exec();
}
```

## [Signals and Slots](https://doc.qt.io/qt-6/signalsandslots.html)
![qt6 offical](https://doc.qt.io/qt-6/images/abstract-connections.png)
- slot    
The fun() declaration is placed in the slot.    
In this case, it is helloQT().
### [connect](https://doc.qt.io/qt-6/qobject.html#connect)
connect a signal to a slot:
- Pointer    
需要关心信号的源头: 从哪里发出的信号
- Macro    
只需要知道发出的是什么信号
