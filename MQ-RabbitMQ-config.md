---
title: "Install and Configure RabbitMQ"
description: "Message Queue2篇：Kafka、RabbitMQ"
date: 2022-05-15T18:08:08+08:00
---

- Ubuntu 18.04    
codename: bionic
- Ubuntu 20.04    
codename: focal


Current Version(RabbitMQ):
```
RabbitMQ 3.10.2 Erlang 24.3.3
```


# Installation
  需要[设置固定IP](https://tdtc-hrb.github.io/csdn/post/ops_network_adapter_linux/)


Replace the "bionic" in the [official website](https://www.rabbitmq.com/install-debian.html#apt-packagecloud) shell with the ubuntu codename.

Note: When saving, the format is Unix(LF).

focal_rq.sh:
```bash
#!/bin/sh

sudo apt-get install curl gnupg apt-transport-https -y

## Team RabbitMQ's main signing key
curl -1sLf "https://keys.openpgp.org/vks/v1/by-fingerprint/0A9AF2115F4687BD29803A206B73A36E6026DFCA" | sudo gpg --dearmor | sudo tee /usr/share/keyrings/com.rabbitmq.team.gpg > /dev/null
## Launchpad PPA that provides modern Erlang releases
curl -1sLf "https://keyserver.ubuntu.com/pks/lookup?op=get&search=0xf77f1eda57ebb1cc" | sudo gpg --dearmor | sudo tee /usr/share/keyrings/net.launchpad.ppa.rabbitmq.erlang.gpg > /dev/null
## PackageCloud RabbitMQ repository
curl -1sLf "https://packagecloud.io/rabbitmq/rabbitmq-server/gpgkey" | sudo gpg --dearmor | sudo tee /usr/share/keyrings/io.packagecloud.rabbitmq.gpg > /dev/null


## Add apt repositories maintained by Team RabbitMQ
sudo tee /etc/apt/sources.list.d/rabbitmq.list <<EOF
## Provides modern Erlang/OTP releases
##
## "focal" as distribution name should work for any reasonably recent Ubuntu or Debian release.
## See the release to distribution mapping table in RabbitMQ doc guides to learn more.
deb [signed-by=/usr/share/keyrings/net.launchpad.ppa.rabbitmq.erlang.gpg] http://ppa.launchpad.net/rabbitmq/rabbitmq-erlang/ubuntu focal main
deb-src [signed-by=/usr/share/keyrings/net.launchpad.ppa.rabbitmq.erlang.gpg] http://ppa.launchpad.net/rabbitmq/rabbitmq-erlang/ubuntu focal main

## Provides RabbitMQ
##
## "focal" as distribution name should work for any reasonably recent Ubuntu or Debian release.
## See the release to distribution mapping table in RabbitMQ doc guides to learn more.
deb [signed-by=/usr/share/keyrings/io.packagecloud.rabbitmq.gpg] https://packagecloud.io/rabbitmq/rabbitmq-server/ubuntu/ focal main
deb-src [signed-by=/usr/share/keyrings/io.packagecloud.rabbitmq.gpg] https://packagecloud.io/rabbitmq/rabbitmq-server/ubuntu/ focal main
EOF

## Update package indices
sudo apt-get update -y
```

exec:
```
chmod +x focal_rq.sh
sudo bash ./focal_rq.sh
```

## Install erlang
  安装Erlang默认使用"Normal"； 如果无法使用Specific.

### Normal
```bash
## Install Erlang packages
sudo apt-get install -y erlang-base \
                        erlang-asn1 erlang-crypto erlang-eldap erlang-ftp erlang-inets \
                        erlang-mnesia erlang-os-mon erlang-parsetools erlang-public-key \
                        erlang-runtime-tools erlang-snmp erlang-ssl \
                        erlang-syntax-tools erlang-tftp erlang-tools erlang-xmerl
```

### Specific
  use ["erlang solutions"](https://www.erlang-solutions.com/downloads/) install specific erlang version.


1. Adding repository entry
```
wget https://packages.erlang-solutions.com/erlang-solutions_2.0_all.deb
sudo dpkg -i erlang-solutions_2.0_all.deb
```

(1) Alternatively: adding the repository entry manually
Add one of the following lines to your /etc/apt/sources.list (according to your distribution):    
```
deb https://packages.erlang-solutions.com/ubuntu focal contrib
```

Next, add the Erlang Solutions public key for "apt-secure" using following commands:    
```bash
wget https://packages.erlang-solutions.com/ubuntu/erlang_solutions.asc
sudo apt-key add erlang_solutions.asc
```

2. Installing Erlang    
There may be problems that the update needs to fix:
```bash
sudo apt --fix-broken install
```

```bash
sudo apt-get update
sudo apt-get install erlang-nox
```



## Install rabbitmq-server
```bash
## Install rabbitmq-server and its dependencies
sudo apt-get install rabbitmq-server -y --fix-missing
```


# Manage
## 1. RabbitMQ service
```bash
sudo systemctl enable rabbitmq-server
sudo systemctl start rabbitmq-server
sudo systemctl stop rabbitmq-server
```

## 2. create user - admin
```bash
sudo rabbitmqctl add_user admin admin
sudo rabbitmqctl set_user_tags admin administrator
sudo rabbitmqctl set_permissions -p / admin ".*" ".*" ".*"
```

## 3. Enable RabbitMQ Management
```bash
sudo rabbitmq-plugins enable rabbitmq_management
```

info:
```bash
tdtc@ubuntu:~$ sudo rabbitmq-plugins enable rabbitmq_management
The following plugins have been configured:
  rabbitmq_management
  rabbitmq_management_agent
  rabbitmq_web_dispatch
Applying plugin configuration to rabbit@ubuntu...
The following plugins have been enabled:
  rabbitmq_management
  rabbitmq_management_agent
  rabbitmq_web_dispatch

started 3 plugins.
```

access [15672 port](192.168.x.x:15672)
![rabbitMQ info](https://gitee.com/xiaobin80/cnblogs/raw/master/images/20200106025932697.png)


# Reference
- [How to Install RabbitMQ Server on Ubuntu 18.04 & 16.04 LTS](https://tecadmin.net/install-rabbitmq-server-on-ubuntu/)
