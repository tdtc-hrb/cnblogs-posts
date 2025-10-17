---
title: "Redis Server installation FAQs"
description: "redis调优"
date: 2022-10-08T08:08:08+08:00
---
> OS: RHEL Community Minimal
- AlmaLinux 8.x
- RockyLinux 8.x
- CentOS 7.9

Root Permission:
```
$su - root
```

# 0) open files

Q: Increased maximum number of open files to 10032 (it was originally set to 1024).
A:
```bash
#ulimit -a
#vi /etc/systemd/system/redis.service
```
In the "[Service]" unit, add the following:
```xml
LimitNOFILE=10032
```

# 1) somaxconn
Q: WARNING: The TCP backlog setting of 511 cannot be enforced because /proc/sys/net/core/somaxconn is set to the lower value of 128.
A:
```bash
#vi /etc/sysctl.conf
```

```xml
net.core.somaxconn=1024
```

# 2) overcommit_memory
Q: WARNING overcommit_memory is set to 0! Background save may fail under low memory condition.
To fix this issue add 'vm.overcommit_memory = 1' to /etc/sysctl.conf and then reboot or run the command 'sysctl vm.overcommit_memory=1'
for this to take effect.

A:
```bash
#echo "vm.overcommit_memory=1" > /etc/sysctl.conf
#reboot
#echo 1 > /proc/sys/vm/overcommit_memory
```

# 3) thp disable
Q: WARNING you have Transparent Huge Pages (THP) support enabled in your kernel.
This will create latency and memory usage issues with Redis.
To fix this issue run the command 'echo never > /sys/kernel/mm/transparent_hugepage/enabled' as root,
and add it to your /etc/rc.local in order to retain the setting after a reboot.
Redis must be restarted after THP is disabled.

A:
reference: https://www.centos.org/forums/viewtopic.php?t=49428
```bash
#vi /etc/rc.d/rc.local
```

```xml
echo never > /sys/kernel/mm/transparent_hugepage/enabled
echo never > /sys/kernel/mm/transparent_hugepage/defrag
```

```bash
#chmod +x /etc/rc.d/rc.local
```

# Addition
```bash
#vi /var/log/redis.log
```
```bash
981:M 02 Sep 05:36:07.544 # Server started, Redis version 3.2.12
981:M 02 Sep 05:36:07.545 # WARNING you have Transparent Huge Pages (THP) support enabled in your kernel. This will create latency and memory usage issues with Redis. To fix this issue run the command 'echo never > /sys/kernel/mm/transparent_hugepage/enabled' as root, and add it to your /etc/rc.local in order to retain the setting after a reboot. Redis must be restarted after THP is disabled.
981:M 02 Sep 05:36:07.550 * DB loaded from disk: 0.005 seconds
981:M 02 Sep 05:36:07.550 * The server is now ready to accept connections on port 6379
981:M 02 Sep 05:38:47.949 # User requested shutdown...
981:M 02 Sep 05:38:47.949 * Saving the final RDB snapshot before exiting.
981:M 02 Sep 05:38:47.964 * DB saved on disk
981:M 02 Sep 05:38:47.964 * Removing the pid file.
981:M 02 Sep 05:38:47.965 # Redis is now ready to exit, bye bye...
969:C 02 Sep 05:39:18.272 # systemd supervision requested, but NOTIFY_SOCKET not found
                _._                                                  
           _.-``__ ''-._                                             
      _.-``    `.  `_.  ''-._           Redis 3.2.12 (00000000/0) 64 bit
  .-`` .-```.  ```\/    _.,_ ''-._                                   
 (    '      ,       .-`  | `,    )     Running in standalone mode
 |`-._`-...-` __...-.``-._|'` _.-'|     Port: 6379
 |    `-._   `._    /     _.-'    |     PID: 978
  `-._    `-._  `-./  _.-'    _.-'                                   
 |`-._`-._    `-.__.-'    _.-'_.-'|                                  
 |    `-._`-._        _.-'_.-'    |           http://redis.io        
  `-._    `-._`-.__.-'_.-'    _.-'                                   
 |`-._`-._    `-.__.-'    _.-'_.-'|                                  
 |    `-._`-._        _.-'_.-'    |                                  
  `-._    `-._`-.__.-'_.-'    _.-'                                   
      `-._    `-.__.-'    _.-'                                       
          `-._        _.-'                                           
              `-.__.-'                                               

978:M 02 Sep 05:39:18.340 # Server started, Redis version 3.2.12
978:M 02 Sep 05:39:18.361 * DB loaded from disk: 0.007 seconds
978:M 02 Sep 05:39:18.361 * The server is now ready to accept connections on port 6379
```
