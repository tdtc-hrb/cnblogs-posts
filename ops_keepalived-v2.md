---
title: "安装配置Keepalived - v2"
description: "本系列包括架设Real Server和Keepalived Server、nginx负载平衡"
date: 2022-10-06T08:38:08+08:00
---
> OS: AlmaLinux/RockyLinux minimal(VBox v6.1.38)

Virtual Box Network configure:
- Attached to
```
Bridged Adapter
```
- Advanced    
promiscuous mode:
```
Allow All
```


# 一、安装
prepare tool:
- RHEL7
```bash
#yum install gcc make autoconf automake openssl-devel libnl3-devel \
    iptables-devel ipset-devel net-snmp-devel libnfnetlink-devel file-devel \
    glib2-devel pcre2-devel libnftnl-devel libmnl-devel systemd-devel kmod-devel
```
- RHEL8    
powertools:
```bash
#dnf --enablerepo=powertools install ipset-devel libnfnetlink-devel file-devel libnftnl-devel kmod-devel pcre2-devel
```
none:
```bash
#dnf install gcc make autoconf automake openssl-devel libnl3-devel \
    iptables-devel net-snmp-devel \
    glib2-devel libmnl-devel systemd-devel git
```

LVS manager:
```bash
#dnf install ipvsadm
```

Reboot the machine so that kmod takes effect!    
Otherwise, it will show:
```
Keepalived_healthcheckers[25270]: kmod_module_new_from_lookup failed - err 38
```


## installation
state(vrrp_instance) - config:
- MASTER    
Node 1
- BACKUP    
Node 2

### down and unzip
> Released Version: 2.2.7
```bash
$git clone https://github.com/acassen/keepalived.git
```
for me:
```bash
$git clone https://gitee.com/xiaobin80/keepalived
```

### installation
```bash
$cd keepalived
$./autogen.sh
```
configure:
```bash
$./configure --prefix=/usr
```
make:
```bash
$make
```

Root permission Required
```bash
#make install
```

### setup
```bash
#mkdir -p /etc/keepalived
$sudo mv /etc/keepalived/keepalived.conf.sample /etc/keepalived/keepalived.conf
$sudo cp ./keepalived/etc/init.d/keepalived /etc/init.d/
$sudo chmod 755 /etc/init.d/keepalived
```

- test
```bash
$keepalived -v
```

## Security Options
allow fire wall port:
```bash
#firewall-cmd --permanent --zone=public --add-port=80/tcp
#firewall-cmd --reload
```

### selinux
```bash
#vi /etc/selinux/config
```
更改enforcing为disabled:
```
SELINUX=disabled
```


# 二、配置
- [Scheduling Algorithms](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/load_balancer_administration/s1-lvs-scheduling-vsa)

[Table 4.1. lv_algo Values for Virtual Server](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/load_balancer_administration/ch-initial-setup-vsa#tb-lbalgovalues-VSA)

|Algorithm Name|lv_algo value|
|-|-|
|Round-Robin|rr|
|Weighted Round-Robin|wrr|
|Least-Connection|lc|
|Weighted Least-Connection|wlc|
|Locality-Based Least-Connection|lblc|
|Locality-Based Least-Connection Scheduling with Replication|lblcr|
|Destination Hash|dh|
|Source Hash|sh|
|Source Expected Delay|sed|
|Never Queue|nq|

- [Routing Methods](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/load_balancer_administration/s1-lvs-routing-vsa)
![Nat](https://access.redhat.com/webassets/avalon/d/Red_Hat_Enterprise_Linux-7-Load_Balancer_Administration-en-US/images/3e3904cae0023bd8bf40fd7612095906/lvs-nat-routing.png)

![Direct Routing](https://access.redhat.com/webassets/avalon/d/Red_Hat_Enterprise_Linux-7-Load_Balancer_Administration-en-US/images/d7df0cdedd0bfbb5a5391e0837711d25/lvs-direct-routing.png)

## example
> /etc/keepalived/keepalived.conf

```bash
global_defs {
    max_auto_priority
}

vrrp_instance VI_1 {
    state MASTER
    interface enp0s3
    virtual_router_id 49
    priority 200
    advert_int 1
    authentication {
        auth_type PASS
        auth_pass passw123
    }
    virtual_ipaddress {
        192.168.0.220/24
    }
}

virtual_server 192.168.0.220 80 {
    delay_loop 6
    lb_algo wrr
    lb_kind DR
    persistence_timeout 9600
    protocol TCP

    real_server 192.168.0.127 80 {
        weight 3
        TCP_CHECK {
            connect_timeout 10
            retry 3
            delay_before_retry 3
            connect_port 80
        }
    }

    real_server 192.168.0.122 80 {
        weight 1
        TCP_CHECK {
            connect_timeout 10
            retry 3
            delay_before_retry 3
            connect_port 80
        }
    }
}
```

### priority
- node 1    
```
200
```
- node 2    
```
150
```


# Reference
- [Chapter 2. Keepalived Overview](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/load_balancer_administration/ch-keepalived-overview-vsa)
