---
title: "使用mysqli和mysql查询数据库"
description: "MySQL示例数据库employees"
date: 2021-04-20T20:26:39+08:00
---

# 导入数据
导入MySQL示例数据库[employees](https://github.com/datacharmer/test_db/releases/tag/v1.0.7)

```bash
C:\Users\tdtc\Downloads\test_db>%MYSQL57_HOME%\bin\mysql < employees.sql -u DBAdmin -p
Enter password: *******
INFO
CREATING DATABASE STRUCTURE
INFO
storage engine: InnoDB
INFO
LOADING departments
INFO
LOADING employees
INFO
LOADING dept_emp
INFO
LOADING dept_manager
INFO
LOADING titles
INFO
LOADING salaries

C:\Users\tdtc\Downloads\test_db>
```

# 查询数据
查询固定员工ID的姓名，所属部门，职务

> 以下为创建SQL的过程。

## 1. 查询姓名
```sql
SELECT concat(first_name, ' ' , last_name) as name FROM `employees` WHERE emp_no = 10001
```

## 2. 查询部门ID
```sql
SELECT dept_no FROM `dept_emp` WHERE emp_no = 10001
```

## 3. 查询所在部门名称
```sql
SELECT dept_name
	FROM `departments`
	WHERE dept_no =
		(SELECT dept_no FROM `dept_emp` WHERE emp_no = 10001)
```

## 4. 查询员工姓名以及部门名称
```sql
SELECT
concat(e.first_name, ' ' , e.last_name) as name,
d.dept_name
	FROM `employees` e, `departments` d
	WHERE d.dept_no =
		(SELECT dept_no FROM `dept_emp` WHERE emp_no = 10001)
		 and
		e.emp_no = 10001
```

## 5. 整合SQL
```sql
SELECT
concat(e.first_name, ' ' , e.last_name) as name,
d.dept_name,
t.title
	FROM
	`employees` e,
	`departments` d,
	`titles` t
	WHERE d.dept_no =
		(SELECT dept_no FROM `dept_emp` WHERE emp_no = 10001)
		 and
		e.emp_no = 10001
                 and
		t.emp_no = 10001
```

# mysql

## 1. 连接
> [mysql_connect](http://php.net/manual/en/function.mysql-connect.php)    
(    
    server, // 服务器地址    
    username,  // 用户名    
    password // 密码    
    )

```php
function conn() {
  //$con = mysql_pconnect("localhost","root","qazxsw");
  $conn = mysql_connect("localhost", "DBAdmin", "xbfirst");
  //$conn = mysql_connect("localhost","root","xbfirst80");
  //$conn = mysqli=_connect("localhost","tdtc2014","qazxsw");
  if (!$conn)
  	die('Could not connect: ' . mysql_error());
  return $conn;
}
```

## 2. 查询

### 1) 选择数据库
> [mysql_select_db](http://php.net/manual/en/function.mysql-select-db.php)
(    
    database_name, // 数据库名称    
    link_identifier // 链接ID    
    )

### 2) 查询语句
> [mysql_query](http://php.net/manual/en/function.mysql-query.php)
(    
   query, // 查询语句    
   link_identifier // 链接ID    
   )

### 3) 返回结果
> [mysql_fetch_assoc](http://php.net/manual/en/function.mysql-fetch-assoc.php)
(    
   result  // 结果集    
   )

```php
function myQuery($sql, $conn) {
  if (!$conn)
  	die('Could not connect: ' . mysql_error());

  $db_selected = mysql_select_db("carnumber", $conn);
  $result = mysql_query($sql,$conn);

  while ($row = mysql_fetch_assoc($result)) {
  	$rows[] = $row;
  }

}
```

# mysqli
> 我们使用Procedural style（过程风格）。

## 1. 连接
> [mysqli_connect](http://php.net/manual/en/function.mysqli-connect.php)
(    
   server, // 服务器地址    
   username, // 用户名    
   password // 密码    
   )

```php
function conn_i() {
  $conn_i = mysqli_connect("localhost", "DBAdmin", "xbfirst");
  if (!$conn_i)
    die('Could not connect: ' . mysqli_connect_errno());
  return $conn_i;
}
```

## 2. 查询

### 1) 选择数据库
> 与mysql参数是颠倒的。    
[mysqli_select_db](http://php.net/manual/en/mysqli.select-db.php)
(    
   link_identifier, // 链接ID    
   database_name // 数据库名称    
   )

### 2) 查询语句
> 与mysql参数是颠倒的。    
[mysqli_query](http://php.net/manual/en/mysqli.query.php)
(    
   link_identifier // 链接ID    
   query // 查询语句    
   )

### 3) 返回结果
> [mysqli_fetch_assoc](http://php.net/manual/en/mysqli-result.fetch-assoc.php)    
(    
   result  // 结果集    
   )

```php
function myQuery_i($sql_i, $conn_i) {
  if (!$conn_i)
    die('Could not connect: ' . mysqli_connect_errno());

  $db_selected = mysqli_select_db($conn_i, "employees");

  $result_i = mysqli_query($conn_i, $sql_i);

  $rows_i = array();

  if($result_i === FALSE) {
    die("--not data source--"); // TODO: better error handling
  }

  while(null !== ($row_i = mysqli_fetch_assoc($result_i)))
  {
    $rows_i[] = $row_i;
  }

  foreach($rows_i as $row_i)
  {
     echo 'name:    ' . $row_i['name'] . '</br>';
     echo 'department: ' . $row_i['dept'] . '</br>';
     echo 'title:    ' . $row_i['title'];
  }

  /* free result set */
  mysqli_free_result($result_i);

}
```

# 附
> test.php

```php
<?php
  header("Content-Type: text/html; charset=utf-8");
  phpinfo();
  testQuery();

function testQuery() {
	$sql = "SELECT
						concat(e.first_name, ' ' , e.last_name) as name,
						d.dept_name as dept,
						t.title as title
					FROM
						`employees` e,
						`departments` d,
						`titles` t
					WHERE
						d.dept_no =
							(SELECT dept_no FROM `dept_emp` WHERE emp_no = 10001)
		 				and
						e.emp_no = 10001
            and
						t.emp_no = 10001";

	if (version_compare(PHP_VERSION, '5.5.0') >= 0) {
		  $conn_i = conn_i();
	    myQuery_i($sql, $conn_i);
	    closeConn_i($conn_i);
	}
	else {
		  $conn = conn();
	    myQuery($sql, $conn);
	    closeConn($conn);
  }
}

function conn() {
  //$con = mysql_pconnect("localhost","root","qazxsw");
  $conn = mysql_connect("localhost", "DBAdmin", "xbfirst");
  //$conn = mysql_connect("localhost","root","xbfirst80");
  //$conn = mysql_connect("localhost","tdtc2014","qazxsw");
  if (!$conn)
  	die('Could not connect: ' . mysql_error());
  return $conn;
}

function closeConn($conn) {
	mysql_close($conn);
}

function myQuery($sql, $conn) {
  if (!$conn)
  	die('Could not connect: ' . mysql_error());

  $db_selected = mysql_select_db("employees", $conn);
  $result = mysql_query($sql,$conn);

  while ($row = mysql_fetch_assoc($result)) {
  	$rows[] = $row;
  }

  foreach ($rows as $r) {
  	 echo 'name:    ' . $r['name'] . '</br>';
     echo 'department: ' . $r['dept'] . '</br>';
     echo 'title:    ' . $r['title'];
  }

}

function conn_i() {
  $conn_i = mysqli_connect("localhost", "DBAdmin", "xbfirst");
  if (!$conn_i)
    die('Could not connect: ' . mysqli_connect_errno());
  return $conn_i;
}

function closeConn_i($conn_i) {
  mysqli_close($conn_i);
}

function myQuery_i($sql_i, $conn_i) {
  if (!$conn_i)
    die('Could not connect: ' . mysqli_connect_errno());

  $db_selected = mysqli_select_db($conn_i, "employees");

  $result_i = mysqli_query($conn_i, $sql_i);

  $rows_i = array();

  if($result_i === FALSE) {
    die("--not data source--"); // TODO: better error handling
  }

  while(null !== ($row_i = mysqli_fetch_assoc($result_i)))
  {
    $rows_i[] = $row_i;
  }

  foreach($rows_i as $row_i)
  {
     echo 'name:    ' . $row_i['name'] . '</br>';
     echo 'department: ' . $row_i['dept'] . '</br>';
     echo 'title:    ' . $row_i['title'];
  }

  /* free result set */
  mysqli_free_result($result_i);

}

?>
```

运行效果：    
![php info](https://gitee.com/xiaobin80/csdn/raw/master/images/20160102192139273.png)


> 关淤：
1. utf-8
```php
header("Content-Type: text/html; charset=utf-8");
```
>> 在日文系统测试上图红框中name和title会出现乱码。    
在html头中加入utf-8编码即可解决。

- [header函数](http://php.net/manual/zh/function.header.php)
```php
void header ( string $string [, bool $replace = true [, int $http_response_code ]] )
```

# 参考文章
- [MySQL vs. MySQLi: Is the Improvement Right for You?](https://blog.udemy.com/mysql-vs-mysqli/)
- [MySQL vs MySQLi in PHP](http://phppot.com/php/mysql-vs-mysqli-in-php/)
- [MySQL 官方示例数据库安装](http://blog.csdn.net/u011877833/article/details/41205133)
- [mysqli_fetch_array()/ mysqli_fetch_assoc()/ mysqli_fetch_row()期望参数1是resource或mysqli_result,boolean给定](https://codeday.me/bug/20170219/1011.html)
