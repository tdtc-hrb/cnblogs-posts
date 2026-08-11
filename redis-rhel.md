---
title: "Install Redis from Source"
date: 2022-10-07T04:58:39+08:00
tags: ["Redis", "Development Tools", "wget", "RHEL"]
---
> OS: RHEL Community Minimal
- AlmaLinux 8.x
- RockyLinux 8.x
- CentOS 7.9

# prepare
- [User set](https://tdtc-hrb.github.io/csdn/post/ops_sudo/)
- [Firewall](https://tdtc-hrb.github.io/csdn/post/ops_firewall/)

dependencies:
```bash
$sudo dnf group install 'Development Tools'
$sudo dnf install tcl wget
```

## v6+
Just to clarify:    
there is no way this issue will be fixed, Redis >= 6 will require a C11 compiler.

- RHEL7
```bash
sudo dnf install centos-release-scl
sudo dnf install devtoolset-8
scl enable devtoolset-8 bash
```

## verification
```bash
$gcc -v
$make -v
$echo 'puts [info patchlevel];exit 0' | tclsh
```


# installation
```bash
$wget https://download.redis.io/redis-stable.tar.gz
$tar xzvf redis-stable.tar.gz
$cd redis-stable
$make
## Single Core
$taskset -c 0 make test
## sudo
$sudo make install
```

## configure
```bash
$sudo mkdir /etc/redis
$sudo mkdir -p /var/redis
$sudo cp /home/tdtc/redis-stable/redis.conf /etc/redis
```

/etc/redis/redis.conf:
```bash
port  6379                       #default port is already 6379.
daemonize yes                    #run as a daemon
supervised systemd               #signal systemd
pidfile /var/run/redis_6379.pid  #specify pid file
loglevel notice                  #server verbosity level
logfile /var/log/redis.log       #log file name
dir  /var/redis/                 #redis directory
```


# service
You do not need to execute the following commands when you are not maintaining it, only the first time.
```bash
$sudo systemctl start redis
$sudo systemctl enable redis
$sudo systemctl status redis
```

Edit:
```bash
$sudo vi /etc/systemd/system/redis.service
```

## v6-
```bash
[Unit]
Description=Redis In-Memory Data Store
After=network.target
[Service]
User=root
Group=root
ExecStart=/usr/local/bin/redis-server /etc/redis/redis.conf
ExecStop=/usr/local/bin/redis-cli shutdown
Restart=always
Type=Forking
LimitNOFILE=10032
[Install]
WantedBy=multi-user.target
```

## v6+
> based on /redis-stable/utils/systemd-redis_server.service
```
[Unit]
Description=Redis Server v6 plus
Wants=network-online.target
After=network-online.target

[Service]
ExecStart=/usr/local/bin/redis-server /etc/redis/redis.conf
LimitNOFILE=10032
NoNewPrivileges=yes
Type=forking
UMask=0077

[Install]
WantedBy=multi-user.target

```

test:
![win cmd](https://github.com/tdtc-hrb/csdn/raw/master/images/20180902055948739.png)


# Reference
- [How to Install Redis Server in CentOS and Debian Based Systems](https://www.tecmint.com/install-redis-server-in-centos-ubuntu-debian/)
- [Build fails under GCC](https://github.com/redis/redis/issues/6286)
- [Developer Toolset 8](https://www.softwarecollections.org/en/scls/rhscl/devtoolset-8/)
- [Centos8 安装 Redis6.0.16](https://www.macnp.com/express/info/8134d10f)
- [Install Redis from Source](https://redis.io/docs/getting-started/installation/install-redis-from-source/)
