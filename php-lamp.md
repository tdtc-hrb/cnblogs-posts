---
title: "安装LAMP"
description: "本文章有两篇：下篇httpd配置"
date: 2020-04-13T03:28:08+08:00
---
> 操作系统：CentOS7.6(x86_64)    
安装方式：源码安装 & RPM包     
安装目录：/opt/      
>>安装文件：     
(1) Apache Httpd [V2.4.x](http://mirror.bit.edu.cn/apache//httpd/httpd-2.4.39.tar.gz)     
(2) MariaDB V5.5(系统自带)    
(3) PHP [V5.6.x](https://www.php.net/distributions/php-5.6.40.tar.gz)

# 一、Httpd
卸载系统自带httpd（V2.4.6-89）
```bash
sudo yum remove httpd
```

## 1. depend soft

## APR
[down](http://mirrors.tuna.tsinghua.edu.cn/apache//apr/apr-1.7.0.tar.gz)
### install
```bash
$./configure --prefix=/opt/apr
$make && sudo make install
```

## APR-util
> Tips: https://bbs.csdn.net/topics/392191188    

[down](http://mirrors.tuna.tsinghua.edu.cn/apache//apr/apr-util-1.6.1.tar.gz)

### expat-devel
```bash
sudo yum install expat-devel
```
### install apr-util
```bash
$./configure --prefix=/opt/apr-util \
	    --with-apr=/opt/apr \
	    --with-expat=/usr/local/expat
$make && sudo make install
```

## install httpd

### pcre-devel
```bash
sudo yum -y install pcre-devel
```

## Setup
```bash
$ tar zxvf httpd-2.4.39.tar.gz
$ <span class="hljs-built_in">cd</span> httpd-2.4.39
$ ./configure --prefix=/opt/httpd \
	    --with-apr-util=/opt/apr-util \
	    --<span class="hljs-built_in">enable</span>-so \
        --<span class="hljs-built_in">enable</span>-rewrite
$ make
$ sudo make install
```


# 二、PHP

## 依赖包安装
```bash
sudo yum install libxml2-devel \
            curl-devel \
            libjpeg-devel \
            libpng-devel
```
如果系统没有安装普通包，则安装之。默认已安装。    
安装libmcrypt
```bash
sudo yum -y install epel-release
```
```bash
sudo yum install libmcrypt libmcrypt-devel -y
```

## 安装php
```bash
tar zxvf php-5.6.40.tar.gz
cd php-5.6.40
./configure --prefix=/opt/php \
    --with-apxs2=/opt/httpd/bin/apxs \
    --with-curl \
    --with-mcrypt \
    --enable-mbstring \
    --with-iconv \
    --with-gd \
    --with-jpeg-dir=/usr/local/lib \
    --enable-pdo \
    --with-pdo-mysql=mysqlnd \
    --with-mysqli=mysqlnd
make
sudo make install
```

### 建立ini文件
当前目录为：php-5.6.40
```bash
sudo cp php.ini-development /opt/php/lib/php.ini
```

### 建立软链接
```bash
sudo ln -s /opt/php/bin/php /usr/bin/php
```

配置httpd和php请参见《配置httpd - linux》
