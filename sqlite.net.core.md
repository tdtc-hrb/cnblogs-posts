---
title: "SQLite在.net core的demo"
description: "使用sqlite-net-sqlcipher"
date: 2021-11-19T19:18:08+08:00
---
IDE: VS2022    
语言: C#    

简介：
  本示例使用SQLite的外键功能。
  <p style="text-decoration: line-through;">密码</p>
# 一、工程设置
## 1. NuGet
> Tools -> NuGet Package Manager -> Package Manager Console
![sqlite-net-pcl](https://gitee.com/xiaobin80/cnblogs/raw/master/images/20201016115835.png)
```bash
Install-Package sqlite-net-pcl -Version 1.8.116
```
![sqlite-net-sqlcipher](https://gitee.com/xiaobin80/cnblogs/raw/master/images/20201016121731.png)
```bash
Install-Package sqlite-net-sqlcipher -Version 1.8.116
```


完整的窗体：
![program ui](https://gitee.com/xiaobin80/cnblogs/raw/master/images/20201016122849.png)
## 2. 控件
  拖拽4个TextBox、4个Button、1个DataGridView

# 二、功能实现
  使用Synchronous API来实现连接、factoryCar表的查询、插入等操作。
## 0. Entity class
> 注意：主键和外键

  建立两个实体类：[FactoryCar](https://gitee.com/xiaobin80/test-sqlite-net-sqlcipher/blob/master/FactoryCar.cs)和[MarqueCar](https://gitee.com/xiaobin80/test-sqlite-net-sqlcipher/blob/master/MarqueCar.cs)

## 1. 连接
```c sharp
asyncConn = new SQLite.SQLiteAsyncConnection(options);
```
其中，options是SQLiteConnectionString实例化的字符串。
```c sharp
var options = new SQLiteConnectionString(edtDB.Text, true,
                                key: edtPwd.Text,
                                preKeyAction: db => db.Execute("PRAGMA cipher_default_use_hmac = OFF;"),
                                postKeyAction: db => db.Execute("PRAGMA kdf_iter = 128000;"));
```
## 2. 查询

### 异步查询
```c sharp
var factoryCars = asyncConn.Table<FactoryCar>().Where(fc => fc.Name.Contains(edtStmt.Text));
```
转换为List类型。
```c sharp
var result = await factoryCars.ToListAsync();
```

### 建立数据表
```c sharp
  table = new System.Data.DataTable("factoryCar");
```

### 指定数据源
```c sharp
  dataGridView1.DataSource = table;
```

## 3. 插入
也可以执行Update、Delete等DML语句。
```c sharp
asyncConn.ExecuteScalarAsync<FactoryCar>(edtSql.Text);
```

# 源代码
## Form1.cs
```c sharp
using System;
using System.Windows.Forms;
using SQLite;

namespace test_sqlite_net_sqlcipher
{
    public partial class Form1 : Form
    {
        private SQLite.SQLiteAsyncConnection asyncConn;

        private System.Data.DataTable table;

        public Form1()
        {
            InitializeComponent();
        }

        private void btnGen_Click(object sender, EventArgs e)
        {
            asyncConn.CreateTableAsync<FactoryCar>();
            asyncConn.CreateTableAsync<MarqueCar>();
        }

        private void btnConn_Click(object sender, EventArgs e)
        {
            var options = new SQLiteConnectionString(edtDB.Text, true,
                                key: edtPwd.Text,
                                preKeyAction: db => db.Execute("PRAGMA cipher_default_use_hmac = OFF;"),
                                postKeyAction: db => db.Execute("PRAGMA kdf_iter = 128000;"));
            asyncConn = new SQLite.SQLiteAsyncConnection(options);
        }

        private async void btnSearch_Click(object sender, EventArgs e)
        {
            var factoryCars = asyncConn.Table<FactoryCar>().Where(fc => fc.Name.Contains(edtStmt.Text));

            var result = await factoryCars.ToListAsync();

            table = new System.Data.DataTable("factoryCar");

            table.Columns.Add("sn", System.Type.GetType("System.Int32"));
            table.Columns.Add("name", System.Type.GetType("System.String"));
            table.Columns.Add("acronym", System.Type.GetType("System.String"));

            foreach (var factoryCar in result)
            {
                long sn = (long)factoryCar.Sn;
                string strName = (string)factoryCar.Name;
                string strAcronym = (string)factoryCar.Acronym;

                table.Rows.Add(sn, strName, strAcronym);
            }

            dataGridView1.DataSource = table;
        }

        private void btnExec_Click(object sender, EventArgs e)
        {
            asyncConn.ExecuteScalarAsync<FactoryCar>(edtSql.Text);
        }
    }
}
```

[gitee testSqlite-core](https://gitee.com/xiaobin80/test-sqlite-net-sqlcipher.git)

# [sqlite-net-sqlcipher](https://github.com/praeclarum/sqlite-net)
以PCL为基础的一套组件。

## PCL(Portable Class Library)
  是过时的!现在的.NET Standard已经包括了。
```bash
PCL->.net standard->.net 5.0+
```

[PCL兼容性](https://docs.microsoft.com/zh-cn/dotnet/standard/net-standard#pcl-compatibility)


## [Visual Studio vs .net framework](https://docs.microsoft.com/en-us/dotnet/framework/migration-guide/versions-and-dependencies#version-information)
|VS|.net framework|Windows|
|-|-|-|
|2019|4.7.2|10(1803/1809)|
|2017|4.6.1|10(1511)|
|2015|4.6|10|
|2013|4.5.1|8.1|
|2012|4.5|8|
|2010|4.0|N/A|
|2008|3.5|7|
|2005|2.0|N/A|
|2003|1.1|N/A|
