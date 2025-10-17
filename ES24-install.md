---
title: "Elasticsearch 2.4 安装"
description: "资源占用比较少的版本V2.4"
date: 2022-05-25T18:08:08+08:00
---
> Ubuntu 20.04.1

# Part I. Elasticsearch

## 1. install JDK
### 1) down jdk8
```bash
wget https://github.com/adoptium/temurin8-binaries/releases/download/jdk8u332-b09/OpenJDK8U-jdk_x64_linux_hotspot_8u332b09.tar.gz
```
unzip:
```bash
$sudo mkdir /usr/java
$sudo tar zxvf OpenJDK8U-jdk_x64_linux_hotspot_8u332b09.tar.gz -C /usr/java
```

### 2) setup
> root:    
>> $sudo -sH

```bash
$echo 'export JAVA_HOME=/usr/java/jdk8u332-b09' >> /etc/bash.bashrc
$echo 'export CLASSPATH=$JAVA_HOME/lib/:.' >> /etc/bash.bashrc
$echo 'export PATH=$PATH:$JAVA_HOME/bin' >> /etc/bash.bashrc
$source /etc/bash.bashrc
```

### 3) test
```bash
java -version
echo $JAVA_HOME
```

## 2. Elasticsearch exec pack

### 1) down
```bash
curl -L -O https://download.elastic.co/elasticsearch/release/org/elasticsearch/distribution/tar/elasticsearch/2.4.6/elasticsearch-2.4.6.tar.gz
```
unzip:
```bash
tar -xvf elasticsearch-2.4.6.tar.gz
```

### 2) config
> ../config/elasticsearch.yml

#### network
```bash
network.host: 0.0.0.0
http.port: 9200
```

#### bootstrap
```bash
bootstrap.system_call_filter: false

bootstrap.seccomp: false
```

#### max map count
[/etc/sysctl.conf](https://www.elastic.co/guide/en/elasticsearch/reference/current/vm-max-map-count.html)
```bash
vm.max_map_count=262144
```

### 3) exec
```bash
cd elasticsearch-2.4.6/bin
./elasticsearch
```
#### shutdown
```bash
Ctrl+c
```

# Part II. Elasticsearch Web Manager

## 1. elasticsearch-head(mobz)
```bash
cd elasticsearch-2.4.6
cd plugins
```

```bash
git clone https://github.com/mobz/elasticsearch-head.git
```

## 2. Run
```bash
cd elasticsearch-2.4.6/bin
./elasticsearch
```

http://localhost:9200/_plugin/elasticsearch-head/
![browser info](https://gitee.com/xiaobin80/cnblogs/raw/master/images/20181130041740029.png)

# FAQ
## 1. WSL
[ES Version > 2.4.x](https://github.com/Microsoft/WSL/issues/3126)
```bash
Ran course. Someone motivated could open a well-formed issue following CONTRIBUTING.md along the lines of #1809. "much /proc/sys/vm/* missing" or somesuch. If there is a memory ballooning issue with ElasticSearch (or anything else), ditto.
```

# Reference
- [zipkin+kafka+elasticsearch](http://www.cnblogs.com/cowboys/p/7602755.html)
