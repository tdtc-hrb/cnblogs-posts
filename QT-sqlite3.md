---
title: "在qt6使用sqlite3"
description: "qt6内置的sqlite"
date: 2021-06-18T00:08:08+08:00
---

# lib
  导入静态库
```c++
#pragma comment(lib, "Qt6Sql.lib")
```

# query data
>
```c++
#include <QtSql/QSqlDatabase>
#include <QtSql/QSqlQueryModel>
```

  首先，设置数据库类型；然后，打开数据库；最后，关联TableView。
```c++
QSqlDatabase db = QSqlDatabase::addDatabase("QSQLITE");
```
## open db
  先设置数据库名，再打开db。
```c++
db.setDatabaseName("SerialComm.db");
db.open();
```

## model-view
  先建立Query对象，再设置Sql语句。
```c++
QSqlQueryModel *model = new QSqlQueryModel;
model->setQuery("SELECT * FROM com1");
```
  最后，设置关联。
```c++
ui.tableView->setModel(model);
```

![qt6 sqlite3 demo](https://gitee.com/xiaobin80/cnblogs/raw/master/images/20210620233610.png)

# src

## QtSqlite3Demo.ui
```xml
<?xml version="1.0" encoding="UTF-8"?>
<ui version="4.0">
 <class>QtSqlite3DemoClass</class>
 <widget class="QMainWindow" name="QtSqlite3DemoClass">
  <property name="geometry">
   <rect>
    <x>0</x>
    <y>0</y>
    <width>600</width>
    <height>400</height>
   </rect>
  </property>
  <property name="windowTitle">
   <string>QtSqlite3Demo</string>
  </property>
  <widget class="QWidget" name="centralWidget">
   <widget class="QPushButton" name="btnSearch">
    <property name="geometry">
     <rect>
      <x>290</x>
      <y>30</y>
      <width>75</width>
      <height>24</height>
     </rect>
    </property>
    <property name="text">
     <string>Search</string>
    </property>
   </widget>
   <widget class="QLineEdit" name="lineEdit">
    <property name="geometry">
     <rect>
      <x>20</x>
      <y>30</y>
      <width>251</width>
      <height>22</height>
     </rect>
    </property>
   </widget>
   <widget class="QTableView" name="tableView">
    <property name="geometry">
     <rect>
      <x>20</x>
      <y>81</y>
      <width>561</width>
      <height>241</height>
     </rect>
    </property>
   </widget>
  </widget>
  <widget class="QMenuBar" name="menuBar">
   <property name="geometry">
    <rect>
     <x>0</x>
     <y>0</y>
     <width>600</width>
     <height>22</height>
    </rect>
   </property>
  </widget>
  <widget class="QToolBar" name="mainToolBar">
   <attribute name="toolBarArea">
    <enum>TopToolBarArea</enum>
   </attribute>
   <attribute name="toolBarBreak">
    <bool>false</bool>
   </attribute>
  </widget>
  <widget class="QStatusBar" name="statusBar"/>
 </widget>
 <layoutdefault spacing="6" margin="11"/>
 <resources>
  <include location="QtSqlite3Demo.qrc"/>
 </resources>
 <connections/>
</ui>

```

## QtSqlite3Demo.h
```c++
#pragma once

#include <QtWidgets/QMainWindow>
#include "ui_QtSqlite3Demo.h"
#include <QtWidgets/QMessageBox>

class QtSqlite3Demo : public QMainWindow
{
    Q_OBJECT

public:
    QtSqlite3Demo(QWidget *parent = Q_NULLPTR);

private slots:
	void sqlite_search();

private:
    Ui::QtSqlite3DemoClass ui;
};

```

## QtSqlite3Demo.cpp
```c++
#include "QtSqlite3Demo.h"
#include <QtSql/QSqlDatabase>
#include <QtSql/QSqlQueryModel>

#pragma comment(lib, "Qt6Sql.lib")

QtSqlite3Demo::QtSqlite3Demo(QWidget* parent)
	: QMainWindow(parent)
{
	ui.setupUi(this);

	//
	connect(ui.btnSearch, SIGNAL(clicked()), this, SLOT(sqlite_search()));
}

void QtSqlite3Demo::sqlite_search()
{
	QString strDbName = ui.lineEdit->text();

	QSqlDatabase db = QSqlDatabase::addDatabase("QSQLITE");

	QString dbPath = qApp->applicationDirPath() + "/" + strDbName;

	db.setDatabaseName(dbPath);
	if (!db.open()) {
		QMessageBox::critical(nullptr, QObject::tr("Cannot open database"),
			QObject::tr("Unable to establish a database connection.\n"
				"Click Cancel to exit."), QMessageBox::Cancel);
	}

	QSqlQueryModel* model = new QSqlQueryModel;
	model->setQuery("SELECT * FROM com1");

	ui.tableView->setModel(model);
}

```


# Reference
- [Detailed Description](https://doc.qt.io/qt-6/qsqlquerymodel.html#details) - QT Offical
- [QT读写Sqlite数据库的三种方式](https://blog.csdn.net/lms1008611/article/details/81271712)
- [Relative paths](https://forum.qt.io/topic/74806/open-file-path)
