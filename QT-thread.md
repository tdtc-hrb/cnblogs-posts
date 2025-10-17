---
title: "QT5简单thread"
description: "qt console"
date: 2025-01-23T22:08:08+08:00
---

# Project
使用Qt Visual Studio Tools创建, 以下简称qt tools。

also see: [QT VS Tools的设置](https://tdtc-hrb.github.io/cnblogs/post/qt-hello/)
## install qt tools
Tools -> Extensions and Updates    
选择Online, 输入"Qt Visual Studio Tools"

## Create Qt Project
File -> New -> Project    
Qt Console Application

## Add class
点击“工程名”右键, Add -> Class    
Class name: Controller    
Base Class: QObject

初始状态的Controller类:    
Controller.h
```c++
#pragma once
#include <qobject.h>
class Controller :
    public QObject
{
};
```

Controller.cpp
```c++
#include "Controller.h"
```


# Worker
此Worker使用的“easy worker”的案例.

## Header
```c++
#include <QObject>

class Worker : public QObject {
	Q_OBJECT

public:
	Worker();
	~Worker();

public slots:
	void process();

signals:
	void finished();
	void error(QString err);

private:
	// add your variables here
};
```

## Class
```c++
#include "Worker.h"

// --- CONSTRUCTOR ---
Worker::Worker() {
	// you could copy data from constructor arguments to internal variables here.
}

// --- DECONSTRUCTOR ---
Worker::~Worker() {
	// free resources
}

// --- PROCESS ---
// Start processing data.
void Worker::process() {
	// allocate resources using new here
	qDebug("Hello World!");
	emit finished();
}
```



# Controller

## Header
申明errorString槽, 以对接Worker的信号“error”;
申明operate信号, 以供操作Controller。
```c++
#pragma once

#include <QObject>

class Controller : public QObject
{
	Q_OBJECT

	QThread* thread;
public:
	Controller(QObject *parent);
	~Controller();

private slots:
	void errorString(QString err);

signals:
	void operate(const QString &s);

};
```

## Class
```c++
#include "Controller.h"

#include <QThread>
#include "Worker.h"

Controller::Controller(QObject *parent)
	: QObject(parent)
{
	thread = new QThread;
	Worker* worker = new Worker();
	worker->moveToThread(thread);
	connect(worker, SIGNAL(error(QString)), this, SLOT(errorString(QString)));
	connect(thread, SIGNAL(started()), worker, SLOT(process()));
	connect(worker, SIGNAL(finished()), thread, SLOT(quit()));
	connect(worker, SIGNAL(finished()), worker, SLOT(deleteLater()));
	connect(thread, SIGNAL(finished()), thread, SLOT(deleteLater()));
	thread->start();
}

Controller::~Controller()
{
	thread->quit();
	thread->wait();
}

void Controller::errorString(QString err)
{

}

```

# main
main.cpp:
```c++
#include <QtCore/QCoreApplication>

#include "Controller.h"

int main(int argc, char *argv[])
{
    QCoreApplication a(argc, argv);

	QObject* obj = new QObject;
	Controller* controller = new Controller(obj);

    return a.exec();
}
```

# Reference
- [easy worker](https://mayaposch.wordpress.com/2011/11/01/how-to-really-truly-use-qthreads-the-full-explanation/)
