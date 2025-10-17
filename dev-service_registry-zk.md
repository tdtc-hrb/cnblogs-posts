---
title: "ZooKeeper Installation(Dev)"
description: "开发用"
date: 2020-05-16T18:08:08+08:00
---

Install ZooKeeper packages
```bash
# sudo apt-get install openjdk-8-jre-headless
# sudo apt-get install zookeeper zookeeperd
```

If you need a cluster, configure the Conf file, otherwise ignore it.
```bash
Configure ZooKeeper

Common Configuration

Edit the /etc/zookeeper/conf/zoo.cfg file to contain the following:

server.1=nsdb1:2888:3888
server.2=nsdb2:2888:3888
server.3=nsdb3:2888:3888
autopurge.snapRetainCount=10
autopurge.purgeInterval =12
[Important]    Important
For production deployments it is recommended to configure the storage of snapshots in a different disk than the commit log, this is done by setting the parameters dataDir and dataLogDir in zoo.cfg. In addition we advice to use an SSD drive for the commit log.

Node-specific Configuration

NSDB Node 1

Create the /var/lib/zookeeper/myid file and edit it to contain the host’s ID:

# echo 1 > /var/lib/zookeeper/myid
NSDB Node 2

Create the /var/lib/zookeeper/myid file and edit it to contain the host’s ID:

# echo 2 > /var/lib/zookeeper/myid
NSDB Node 3

Create the /var/lib/zookeeper/myid file and edit it to contain the host’s ID:

# echo 3 > /var/lib/zookeeper/myid
```

Restart ZooKeeper
```bash
# sudo service zookeeper restart
```

Verify ZooKeeper Operation
```bash
$ echo ruok | nc 127.0.0.1 2181
imok
```

More detailed information can be requested with the stat command,
which lists statistics about performance and connected clients:
```bash
wsl1017@DESKTOP-14G6K9S:/etc/zookeeper/conf$ echo stat | nc 127.0.0.1 2181
Zookeeper version: 3.4.10-3--1, built on Sat, 03 Feb 2018 14:58:02 -0800
Clients:
 /127.0.0.1:56273[0](queued=0,recved=1,sent=0)

Latency min/avg/max: 0/0/0
Received: 1
Sent: 0
Connections: 1
Outstanding: 0
Zxid: 0x0
Mode: standalone
Node count: 4
```


# Reference
- [ZooKeeper Installation](https://docs.midonet.org/docs/latest/quick-start-guide/ubuntu-1604_mitaka/content/_zookeeper_installation.html)
