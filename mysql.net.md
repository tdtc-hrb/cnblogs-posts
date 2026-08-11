---
title: "MySQL Connector/Net 的简单使用"
description: "组件方式"
date: 2025-03-02T00:08:08+08:00
---
|Connector/NET Version|ADO.NET Version|.NET Version Required|MySQL Server|IDE|
|-|-|-|-|-|
|6.9|2.x+|3.5+ for VS 2008, 4.x+ for VS 2010 / 2012 / 2013, WinRT for VS 2012 / 2013|5.7, 5.6, 5.5|Visual Studio 2010|
|6.1|2.x+|2.x+|5.6, 5.5, 5.1, 5.0, 4.1|Visual Studio 2008|

连接器(For .net)：
- vs2008/2010    
[V6.1.6](https://cdn.mysql.com/archives/mysql-connector-net-6.1/mysql-connector-net-6.1.6.msi)
- [vs2015](https://stackoverflow.com/a/40750607)    
[v6.9.8](https://cdn.mysql.com/archives/mysql-connector-net-6.9/mysql-connector-net-6.9.8.msi)
- [dotnet and mysql connector - vs2019](https://dev.mysql.com/doc/connector-net/en/connector-net-versions.html)    
[v8.0.32](https://cdn.mysql.com/archives/mysql-connector-net-8.0/mysql-connector-net-8.0.32.msi)

### 新建工程 - Windows Application
- vs2008
![vs2008 ui](https://raw.githubusercontent.com/tdtc-hrb/cnblogs/main/images/20200609110346.png)
- vs2019
![vs2019 ui](https://raw.githubusercontent.com/tdtc-hrb/cnblogs/main/images/20250301074031.png)
### 增加引用(MySql.Data)
- vs2008
![vs2008 ui](https://raw.githubusercontent.com/tdtc-hrb/cnblogs/main/images/20200609110557.png)

![vs2008 ui](https://raw.githubusercontent.com/tdtc-hrb/cnblogs/main/images/20200609111014.png)
- vs2019
![vs2019 ui](https://raw.githubusercontent.com/tdtc-hrb/cnblogs/main/images/20250301074737.png)

### 放置控件
  3个TextBox，2个ComboBox(cBoxDBs和cBoxTables)， 1个DataGridView等等
![vs2008 ui](https://raw.githubusercontent.com/tdtc-hrb/cnblogs/main/images/20200609124240.png)


![vs2010 ui](https://raw.githubusercontent.com/tdtc-hrb/cnblogs/main/images/20200402142537984.png)
密码框设置

![vs2010 ui](https://raw.githubusercontent.com/tdtc-hrb/cnblogs/main/images/20200402142805972.png)
下拉框设置（cBoxDBs：数据库，cBoxTables：数据表）

![vs2010 ui](https://raw.githubusercontent.com/tdtc-hrb/cnblogs/main/images/20200402142924594.png)
数据格设置

## 代码
连接按钮：
```c#
string connStr = string.Format("server={0};user id={1}; password={2}; database=mysql; pooling=false",
    edtSrv.Text, edtUser.Text, edtPwd.Text);
try
{
    conn = new MySqlConnection(connStr);
    conn.Open();

    // 获得数据库列表
    List<string> cmd = new List<string>();
    cmd.Add("SHOW DATABASES");
    List<string> list = getDataList(cmd);

    // 清空下拉框
    cBoxDBs.Items.Clear();
    // 增加下拉框列表
    foreach (string str in list)
        cBoxDBs.Items.Add(str);

}
catch(MySqlException ex)
{
    MessageBox.Show("没有找到数据库: " + ex.Message);
}
```

下拉框（数据库列表）
```c#
// 获得数据库列表
List<string> cmdList = new List<string>();
cmdList.Add("USE " + cBoxDBs.SelectedItem.ToString());
cmdList.Add("SHOW TABLES");
List<string> list = getDataList(cmdList);

// 清空下拉框
cBoxTables.Items.Clear();
// 增加下拉框列表
foreach (string str in list)
    cBoxTables.Items.Add(str);
```

下拉框（数据表列表）
```c#
// 获得数据表名称
string tableName = cBoxTables.SelectedItem.ToString();
// 设置数据桥
dataAdapter = new MySqlDataAdapter("Select * from " + tableName, conn);

// DataSet
sqlCmdBuilder = new MySqlCommandBuilder(dataAdapter);
// 建立数据表
table = new DataTable(tableName);
// 填充数据表到数据桥
dataAdapter.Fill(table);

// 指定数据源
dataGridView1.DataSource = table;
```


运行效果：
![vs2008 ui](https://raw.githubusercontent.com/tdtc-hrb/cnblogs/main/images/20200609123745.png)

Form1.cs
```c#
using System;
using System.Collections.Generic;
using System.ComponentModel;
using System.Data;
using System.Drawing;
using System.Linq;
using System.Text;
using System.Windows.Forms;
using MySql.Data.MySqlClient;


namespace testMySQL
{
    public partial class frmain : Form
    {
        private MySqlConnection conn;
        private DataTable table;
        private MySqlDataAdapter dataAdapter;
        private MySqlCommandBuilder sqlCmdBuilder;

        public frmain()
        {
            InitializeComponent();
        }

        private void btnConn_Click(object sender, EventArgs e)
        {
            //
            string connStr = string.Format(
                "server={0}; user id={1}; password={2}; database=mysql; pooling=false",
                edtSrv.Text, edtUser.Text, edtPwd.Text);
            try
            {
                conn = new MySqlConnection(connStr);
                conn.Open();

                // 获得数据库列表
                List<string> cmd = new List<string>();
                cmd.Add("SHOW DATABASES");
                List<string> list = getDataList(cmd);

                // 清空下拉框
                cBoxDBs.Items.Clear();
                // 增加下拉框列表
                foreach (string str in list)
                    cBoxDBs.Items.Add(str);

            }
            catch(MySqlException ex)
            {
                MessageBox.Show("没有找到数据库: " + ex.Message);
            }

        }

        private List<string> getDataList(List<string> cmdList)
        {
            List<string> result = new List<string>();

            // SQL数据读取器
            MySqlDataReader dataReader = null;

            // SQL命令执行器
            MySqlCommand sqlCmd = new MySqlCommand();

            // 设置SQL命令执行器的连接
            sqlCmd.Connection = conn;

            try
            {
                // 执行的SQL命令
                foreach (string cmd in cmdList)
                {
                    sqlCmd.CommandText = cmd;
                    sqlCmd.ExecuteNonQuery();
                }
                //   
                dataReader = sqlCmd.ExecuteReader();

                while(dataReader.Read())
                {
                    string strDbName = dataReader.GetString(0);

                    result.Add(strDbName);
                }
            }
            catch (MySqlException ex)
            {
                MessageBox.Show("读取数据失败: " + ex.Message);
            }
            finally
            {
                if (dataReader != null)
                    dataReader.Close();
            }
            return result;
        }

        private void cBoxDBs_SelectedIndexChanged(object sender, EventArgs e)
        {
            // 获得数据库列表
            List<string> cmdList = new List<string>();
            cmdList.Add("USE " + cBoxDBs.SelectedItem.ToString());
            cmdList.Add("SHOW TABLES");
            List<string> list = getDataList(cmdList);

            // 清空数据表列表            
            cBoxTables.Items.Clear();            
            // 增加下拉框列表            
            foreach (string str in list)                
               cBoxTables.Items.Add(str);        
        }        
        private void cBoxTables_SelectedIndexChanged(object sender, EventArgs e)        
        {            
            // 获得数据表名称            
            string tableName = cBoxTables.SelectedItem.ToString();            
            // 设置数据桥            
            dataAdapter = new MySqlDataAdapter("Select * from " + tableName, conn);            
            // DataSet            
            sqlCmdBuilder = new MySqlCommandBuilder(dataAdapter);            
            // 建立数据表            
            table = new DataTable(tableName);            
            // 填充数据表到数据桥            
            dataAdapter.Fill(table);            
            // 指定数据源            
            dataGridView1.DataSource = table;        
         }    
    }
}

```

## FAQ
1. Unable to convert MySQL date/time value to System.DateTime    
> 出现原因：
   DateTime列含有“0000-00-00”（YYYY-mm-dd）或者“0000-00-00 00:00:00”（YYYY-mm-dd HH:mm:ss）数据
![vs2008 ui](https://raw.githubusercontent.com/tdtc-hrb/cnblogs/main/images/20140314102336328.png)

  解决办法： 增加“Convert Zero Datetime=True”
```c sharp
server={0}; user id={1}; password={2}; database=mysql; pooling=false; Convert Zero Datetime=True
```

2. [Interop type cannot be embedded](https://stackoverflow.com/questions/2483659/interop-type-cannot-be-embedded)    
![vs2008 ui](https://raw.githubusercontent.com/tdtc-hrb/cnblogs/main/images/20180928144823943.png)
