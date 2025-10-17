---
title: "spark安装"
description: "Spark cluster共3篇，其余Spark安装、Spark - cluster"
date: 2022-10-10T00:08:08+08:00
---
- Spark    
v3.1
- OS    
RHEL8(Alma / Rocky Linux)


# 一、基础软件
## 1. R-lang
```bash
$ sudo dnf -y install epel-release
$ sudo dnf  --enablerepo=powertools install R
```
> 注意：如果Java没有安装，会附带着安装OpenJDK。

## 2. Scala
```bash
$ wget https://downloads.lightbend.com/scala/2.12.17/scala-2.12.17.tgz
$ tar xvf scala-2.12.17.tgz
$ sudo mv scala-2.12.17 /usr/lib
$ sudo ln -s /usr/lib/scala-2.12.17 /usr/lib/scala
```

### 1) add path
```bash
$vi ~/.bashrc
```
```bash
PATH=$PATH:/usr/lib/scala/bin
```

# 二、Spark
## 1. down & unzip
> [v3.1.3](https://www.apache.org/dyn/closer.lua/spark/spark-3.1.3/spark-3.1.3-bin-hadoop2.7.tgz)

```bash
$ mkdir -p ~/app/spark
$ tar zxvf spark-3.1.3-bin-hadoop2.7.tgz -C ~/app/spark
$ mv ~/app/spark/spark-3.1.3-bin-hadoop2.7 ~/app/spark/3.1.3
```

## 2. test
```bash
$ cd ~/app/spark/3.1.3
$ ./bin/spark-shell --master local[2]
```
input:
```bash
for (i <- 1 to 3; j <- 1 to 3 if i != j) print(10 * i + j + "\t")
```

input:(exit)
```bash
:q
```

# 三、开启服务
exec:
```bash
$SPARK_HOME/sbin/start-master.sh
```

- [Spark v3.1.3](http://192.168.0.104:8080/)
![2022-10-10 12:15:45](https://github.com/tdtc-hrb/cnblogs/raw/master/images/20221010121645.png)

## 1. 添加系统变量
```bash
$ echo 'export SPARK_HOME=$HOME/app/spark/3.1.3' >> .bash_profile
$ echo 'export PATH=$PATH:$SPARK_HOME/bin' >> .bash_profile
```

```bash
$ source ~/.bash_profile
```

## 2. open port
```bash
sudo firewall-cmd --permanent --zone=public --add-port=6066/tcp
sudo firewall-cmd --permanent --zone=public --add-port=7077/tcp
sudo firewall-cmd --permanent --zone=public --add-port=8080-8088/tcp
```
take effect:
```bash
sudo firewall-cmd --reload
```


# Reference
- [How To Install Apache Spark on CentOS 7](https://idroot.us/linux/install-apache-spark-centos-7/)
- [spark-standalone](http://spark.apache.org/docs/latest/spark-standalone.html)
