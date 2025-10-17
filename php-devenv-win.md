---
title: "Windows搭建PHP环境"
date: 2025-08-04T00:12:08+08:00
---
- [Apache Lounge Downloads](https://www.apachelounge.com/download/)
- [windows openssl](https://slproweb.com/products/Win32OpenSSL.html)

# Apache配置
- ServerRoot
```
Define SRVROOT "C:/Apache24"
```
- ServerName
```
ServerName localhost:80
```

## php-apache
```
Non Thread Safe(NTS) 和 Thread Safe(TS)： "NTS"，没有"php(5/7/8)apache2_4.dll"
```
> Php7.2+ 已移除 mcrypt.

- PHP 8
```bash
PHPIniDir "C:/php-8.3"
LoadModule php_module "C:/php-8.3/php8apache2_4.dll"
```
- Php 5
```
PHPIniDir "C:/php-5.6"
LoadModule php5_module "C:/php-5.6/php5apache2_4.dll"
```
在配置文件(conf/httpd.conf)最后增加:
```
AddType application/x-httpd-php .php
```

- test config
```
httpd -t
```

### Directory Index
sets the file that Apache will serve if a directory is requested.
- append
```
index.php
```

## 重写
- a.打开模块
```bash
LoadModule rewrite_module modules/mod_rewrite.so
```
- b.允许重写
```bash
AllowOverride All
```

## SSL
- open module
```
LoadModule ssl_module modules/mod_ssl.so
LoadModule socache_shmcb_module modules/mod_socache_shmcb.so
```
- at httpd.conf:
```
Include conf/extra/httpd-ssl.conf
```

### [shmcb](https://www.apachelounge.com/viewtopic.php?t=7614)
```
glsmith:
Did you restart the server after uncommenting the LoadModule line for mod_socache_shmcb?
```

### basic conf
> development
>> httpd-ssl.conf

Replace
```
SSLCertificateFile "${SRVROOT}/conf/server.crt"
SSLCertificateKeyFile "${SRVROOT}/conf/server.key"
```
with
```
SSLCertificateFile "${SRVROOT}/conf/ssl/dev.crt"
SSLCertificateKeyFile "${SRVROOT}/conf/ssl/dev.key"
```
copy "dev.crt" and "dev.key" to directory:
```
${SRVROOT}/conf/ssl
```


# [phpMyAdmin](https://www.phpmyadmin.net/)
> 如果使用本机mysql则无需配置！
- 4.9
```
PHP 5.5-7.4
```
- 5.2
```
php v7.2+
```

### 配置远程MySQL
- 建立配置文件    
  重命名config.sample.inc.php为config.inc.php
- 添加服务器信息    
  修改localhost值: 服务器IP。
```bash
$cfg['Servers'][$i]['host'] = '192.168.1.4';
```

### blowfish secret
The configuration file now needs a secret passphrase (blowfish_secret).
- config blowfish_secret
```bash
$cfg['blowfish_secret'] = 'qtdRoGmbc9{8IZr323xYcSN]0s)r$9b_JUnb{~Xz';
```

## Php.ini
- extension设置目录
```bash
extension_dir = "C:/php-8.3/ext"
```
- memory limit:
```
memory_limit = -1
```

### 开启mysql
pdo and mysqli:
```bash
extension=mysqli
extension=pdo_mysql
```
- test.php
```bash
<?php
    header("Content-Type: text/html; charset=utf-8");
    phpinfo();
?>
```
- test_pdo.php:
```
<?php
// save as test_pdo.php

var_dump(PDO::getAvailableDrivers());

$host       = 'localhost';
$dbname     = 'carnumber';
$username   = 'tdtc2014';
$password   = 'qazxsw';

$pdo_object = new PDO("mysql:host=$host;dbname=$dbname", $username, $password);

$query = $pdo_object->query('SELECT * FROM users');

var_dump($query->fetchAll(PDO::FETCH_ASSOC));
```

### [opcache](http://stackoverflow.com/questions/17224798/how-to-use-php-opcache)
在Dynamic Extensions最下面增加：
```bash
zend_extension = php_opcache.dll
```
Note: 如果不加入"opcache.mmap_base = 0x20000000" 
会出现“Fatal Error Unable to open base address file”错误
```bash
[opcache]
opcache.enable = 1
opcache.memory_consumption = 128
opcache.max_accelerated_files = 4000
opcache.revalidate_freq = 60
opcache.mmap_base = 0x20000000

; Required for Moodle
opcache.use_cwd = 1
opcache.validate_timestamps = 1
opcache.save_comments = 1
opcache.enable_file_override = 0
```


# FAQ
注册Windows服务(管理员权限):
```bash
@echo off
echo register begin... ...

set regpath=HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Session Manager\Environment

echo register APACHE2_HOME... ...

set envname1=APACHE2_HOME
set varpath1=C:\Apache24
reg add "%regpath%" /v %envname1% /d %varpath1% /f

echo register MYSQL_HOME... ...

set envname2=MYSQL584_HOME
set varpath2=%USERPROFILE%\mysql-584
reg add "%regpath%" /v %envname2% /d %varpath2% /f

echo register modify path... ...
reg add "%regpath%" /v Path /t REG_EXPAND_SZ /d "%path%;%%APACHE2_HOME%%\bin;%%MYSQL584_HOME%%\bin;" /f
rem wmic ENVIRONMENT where "Name='path' and UserName='<system>'" set VariableValue="%path%;%%APACHE2_HOME%%\bin;%%MYSQL584_HOME%%\bin;"

echo register end... ...

echo Service List Begin... ...

echo Service HTTPD reg... ...
httpd.exe -k install
echo ...
echo Service HTTPD(apache2.4) Start... ...
net start apache2.4


echo Service MYSQLD Reg... ...
rem %MYSQL584_HOME%\bin\mysqld --install MySQL584 --defaults-file=%USERPROFILE%\mysql-584\my.ini
%MYSQL584_HOME%\bin\mysqld -install MySQL584
echo ...
echo Service MYSQLD(mysql5.7/8.0/8.4) Start... ...
net start MySQL584

echo Service List End... ...

pause>nul
```

## Apache Issues
- AH00526
- AH00072
- AH01797
- AH01906

### AH00526
```bash
AH00526: Syntax error on line 233 of C:/Apache24/conf/httpd.conf:
Invalid command 'Order', perhaps misspelled or defined by a module not included in the server configuration
```
A: 去除如下配置语句的注释符。    
[Apache2.4启动时报AH00526错误（Invalid command 'Order'）](http://www.cnblogs.com/haries/p/4677383.html)
```bash
#LoadModule access_compat_module modules/mod_access_compat.so
```

### AH00072
```bash
Errors reported here must be corrected before the service can be started.
(OS 10048)通常每个套接字地址(协议/网络地址/端口)只允许使用一次。  : AH00072: make_sock: could not bind to address [::]:443
(OS 10048)通常每个套接字地址(协议/网络地址/端口)只允许使用一次。  : AH00072: make_sock: could not bind to address 0.0.0.0:443
AH00451: no listening sockets available, shutting down
AH00015: Unable to open logs
```
A: 关闭其他占用443端口的软件。

### [AH01797](https://stackoverflow.com/a/13923526)
```
AH01797: client denied by server configuration
```
httpd-ssl.conf:
```
<Directory "${SRVROOT}/htdocs">
	###Options Indexes Includes FollowSymLinks
	###AllowOverride AuthConfig Limit FileInfo
    Order allow,deny
    Allow from all
    Require all granted
</Directory>
```
instead of
```
<Directory "${SRVROOT}/htdocs">
	Options Indexes Includes FollowSymLinks
	AllowOverride AuthConfig Limit FileInfo
    Require all granted
</Directory>
```

### AH01906
```
localhost:443:0 server certificate is a CA certificate (BasicConstraints: CA == TRUE !?)
```
A: [use interactive](https://tdtc-hrb.github.io/cnblogs/post/ops_openssl)
