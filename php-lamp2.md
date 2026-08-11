---
title: "httpd配置"
description: "本文章有两篇：上篇安装LAMP"
date: 2020-04-13T03:38:08+08:00
---

# 一、Apache2.4+php5.6

## 1. httpd.conf

### DSO
> Line:152

```bash
LoadModule php5_module        modules/libphp5.so
```

### Server Name
> Line:194 - Instead

```bash
ServerName localhost:80
```

### Each Directory
> Line:202 - Instead

```bash
AllowOverride all
```
### Document Root
> Line:239 - Instead

```bash
AllowOverride All
```

### Directory Index
> Line:252 - Instead

```bash
DirectoryIndex index.html index.php
```

### MIME Type
> Line:390 - Add

```bash
AddType application/x-httpd-php .php
```

## 2. php.ini

### Paths and Directories
> Line:709 - Instead

```bash
include_path = "/opt/php/lib/php"
```

### socket
#### [Pdo_mysql]
```bash
pdo_mysql.default_socket = /var/lib/mysql/mysql.sock
```
#### [MySQLi]
> Line:1221 - Instead

```bash
mysqli.default_socket = /var/lib/mysql/mysql.sock
```

# 二、Apache2.2+php5.3
## 1. httpd.conf

- DSO(Dynamic Shard Object)     
 (Line:55 - Add)    
```bash
LoadModule php5_module        modules/libphp5.so
```
- Server Name     
(Line:99 - Instead)    
```bash
ServerName localhost:80
```
- Each Directory     
(Line:118 - Instead)     
```bash
AllowOverride all
```
- Document Root     
(Line:153 - Instead)    
```bash
AllowOverride all
```
- Directory Index    
(Line:168 - Instead)
```bash
DirectoryIndex index.html index.php
```
- MIME Type    
(Line:285 - Add)
```bash
AddType application/x-httpd-php .php
```

## 2. php.ini
- Paths and Directories     
(Line:796 - Instead)
```bash
include_path = "/opt/php/lib/php"
```
- socket     
```bash
Sock file when using RPM default installation location
```
1. [Pdo_mysql]    
(Line:1075 - Instead)
```bash
pdo_mysql.default_socket = /var/lib/mysql/mysql.sock
```
2. [MySQL]     
(Line:1229 - Instead)
```bash
mysql.default_socket = /var/lib/mysql/mysql.sock
```
