---
title: "SQLite在.net core的demo"
description: "使用sqlite-net-base and SQLite3MC.PCLRaw.bundle"
date: 2026-02-19T19:18:08+08:00
---
- .net core    
v8.0+
- test-backup.db    
由 [Windows的SQLite静态库 - wx](https://tdtc-hrb.github.io/com-vc/posts/sqlite3lib-wx) 的 minimal.exe 生成

![program ui](https://gitee.com/xiaobin80/cnblogs/raw/master/images/20201016122849.png)
### NuGet
> pmc: Package Manager Console

- SQLite3MC.PCLRaw.bundle
```pmc
Install-Package SQLite3MC.PCLRaw.bundle
```
Warning! Don't use multiple SQLitePCLRaw bundles in the same project. See the instructions below for details.

![SQLite3MC.PCLRaw.bundle](https://gitee.com/xiaobin80/cnblogs/raw/master/images/20201016121731.png)

- SQLite-net
```pmc
Install-Package sqlite-net-base -Version 1.9.172
```
With SQLite-net, be sure to use the sqlite-net-base package instead of the main one to avoid using multiple bundles.

![sqlite-net-base](https://gitee.com/xiaobin80/cnblogs/raw/master/images/20201016115835.png)

## 1. 连接
SQLite Connection String:
```c sharp
var options = new SQLiteConnectionString(
    edtDB.Text, 
    storeDateTimeAsTicks: true,
    key: edtPwd.Text);
```
- Async Connection
```c sharp
asyncConn = new SQLite.SQLiteAsyncConnection(options);
```
- Sync
```

```
## 2. 查询
- Synchronous API
- Asynchronous API
### 异步查询
```c sharp
var empRecords = connection.Table<Emp>().Where(emp => emp.Empno > 111 && emp.Empno < 666);
```

### show data
```c sharp
  table = new System.Data.DataTable("emp");
```
- 数据源
```c sharp
  dataGridView1.DataSource = table;
```

## 3. 插入
也可以执行Update、Delete等DML语句。
```c sharp
asyncConn.ExecuteScalarAsync<Emp>(edtSql.Text);
```

## 源代码
- Emp.cs
```
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Threading.Tasks;

namespace testSqlite_core
{
    internal class Emp
    {
        public int Empno { get; set; }
        public string Empname { get; set; }
        public decimal Salary { get; set; }
    }
}
```
- Form1.cs
```c sharp
using SQLite;

namespace testSqlite_core
{
    public partial class Form1 : Form
    {
        public Form1()
        {
            InitializeComponent();
        }

        private void button1_Click(object sender, EventArgs e)
        {
            SQLitePCL.Batteries_V2.Init();

            var connection = new SQLiteConnection(new("test-backup.db", storeDateTimeAsTicks: true, key: "password"));

            var empRecords = connection.Table<Emp>().Where(emp => emp.Empno > 111 && emp.Empno < 666);

            var result = empRecords.ToList();

            showDataGrid(result);
        }

        private async void button2_Click(object sender, EventArgs e)
        {
            var options = new SQLiteConnectionString(
                            edtDB.Text,
                            storeDateTimeAsTicks: true,
                            key: edtPwd.Text);

            SQLitePCL.Batteries_V2.Init();

            var asyncConn = new SQLite.SQLiteAsyncConnection(options);

            var empRecords = asyncConn.Table<Emp>().Where(emp => emp.Empno > 1 && emp.Empno < 999);

            var result = await empRecords.ToListAsync();

            showDataGrid(result);
        }

        private void button3_Click(object sender, EventArgs e)
        {
            SQLitePCL.Batteries_V2.Init();

            var connection = new SQLiteConnection(new("test-backup.db", storeDateTimeAsTicks: true, key: "password"));

            var cmd = new SQLiteCommand(connection);
            cmd.CommandText = edtSql.Text;
            var emps = cmd.ExecuteQuery<Emp>();

            showDataGrid(emps.ToList());
        }

        /// <summary>
        /// Display data to DataGridView
        /// </summary>
        /// <param name="list"></param>
        private void showDataGrid(IList<Emp> list)
        {
            var table = new System.Data.DataTable("emp");

            table.Columns.Add("empno", Type.GetType("System.Int32")!);
            table.Columns.Add("empname", Type.GetType("System.String")!);
            table.Columns.Add("salary", Type.GetType("System.Decimal")!);

            foreach (var emp in list)
            {
                var no = emp.Empno;
                var name = emp.Empname;
                var sale = emp.Salary;

                table.Rows.Add(no, name, sale);
            }

            dataGridView1.DataSource = table;
        }
    }
}

```

## Ref
- [sqlite-net](https://github.com/praeclarum/sqlite-net/wiki)
- [SQLite-net-base](https://www.nuget.org/packages/SQLite3MC.PCLRaw.bundle#readme-body-tab)
- [How Can find Data between Two Date Using LINQ](https://learn.microsoft.com/en-us/answers/questions/1658280/how-can-find-data-between-two-date-using-linq)
