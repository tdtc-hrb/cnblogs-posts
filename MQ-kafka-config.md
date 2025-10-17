---
title: "Install and Configure Apache Kafka"
description: "Message Queue2篇：Kafka、RabbitMQ"
date: 2022-05-16T18:08:08+08:00
---

# I. Installation
  The installation environment must have JDK, verify that you enter:
```bash
java -version
```

## 1. down
  Install server-side versions based on the jar downloaded by maven,
such as my "C:\Users\tdtc\.m2\repository\org\apache\kafka\kafka-clients\3.0.1"

  And choose Scala version based on pom,
such as my "C:\Users\tdtc\.m2\repository\org\springframework\cloud\spring-cloud-stream-binder-kafka\3.2.4\spring-cloud-stream-binder-kafka-3.2.4.pom"

```bash
wget https://archive.apache.org/dist/kafka/3.0.1/kafka_2.13-3.0.1.tgz
```

## 2. unzip
```bash
$tar zxvf kafka_2.13-3.0.1.tgz
```

# II. Configuration
> server.properties

## 1. Necessary settings
### 1) basic
#### listener
```bash
listeners=PLAINTEXT://your_IP:9092
```
#### logs
```bash
$mkdir HOME/kafka_2.13-3.0.1/logs
```

### 2) cluster
Sets the natural number of the broker ID to non-zero.    
  Host1:
```bash
broker.id=1
```
Host2:
```bash
broker.id=2
```
Host3:
```bash
broker.id=3
```

## 2. Optional settings
### 1) Zookeeper
default
```bash
## Zookeeper ##
zookeeper.connect: The ZooKeeper address (can list multiple addresses comma-separated for the ZooKeeper cluster).

zookeeper.connection.timeout.ms: Time to wait before going down if, for some reason, the broker is not able to connect.
```

### 2) Socket Server Settings
default
```bash
## Socket Server Settings ##
socket.send.buffer.bytes: The send buffer used by the socket server.

socket.receive.buffer.bytes: The socket server receives a buffer for network requests.

socket.request.max.bytes: The maximum request size the server will allow. This prevents the server from running out of memory.
```

### 3) Log Flush Policy
default
```bash
## Log Flush Policy ##
log.flush.interval.messages: Threshold for message count that is once reached all messages are flushed to the disk.

log.flush.interval.ms: Periodic time interval after which all messages will be flushed into the disk.
```

### 4) Log Retention Policy
default
```bash
## Log Retention Policy ##

log.retention.hours: The minimum age of the segment file to be eligible for deletion due to age.

log.retention.bytes: A size-based retention policy for logs. Segments are pruned from the log unless the remaining segments drop below log.retention.bytes.

log.segment.bytes: Size of the segment after which a new segment will be created.

log.retention.check.interval.ms: Periodic time interval after which log segments are checked for deletion as per the retention policy. If both retention policies are set, then segments are deleted when either criterion is met.
```

# III. Run
Set Kafka Home:
```bash
$sudo vi /etc/bashrc
```

```bash
export KA_HOME=/home/tdtc/app/kafka_2.13-3.0.1
export PATH=$PATH:$KA_HOME/bin
```

## launch
1. start
```bash
kafka-server-start.sh $KA_HOME/config/server.properties
```

2. stop
```bash
kafka-server-stop.sh
```

# Reference
- [How to Set Up Kafka](https://dzone.com/articles/kafka-setup)
- [How to Setup Kafka Cluster](https://dzone.com/articles/how-to-setup-kafka-cluster)
