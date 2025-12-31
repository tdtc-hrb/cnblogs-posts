---
title: "最新版的Debian网络Adapter"
date: 2025-12-31T21:08:39+08:00
---
- Debian 13.x

#### Unattended Installation
Cancel "Proceed with Unattended Installation"

在使用自动无人值守安装时，会自动安装桌面的Debian

### net adapter
设置Network Adapter为Bridged
- vmware(25h2)    
 default: ens32
>> failed
- vbox    
 default: enp0s3
 
### netplan.io
```
sudo apt install netplan.io
```
- High version    
If you get an error about systemd-resolved not being found/installed. Install it before proceeding:
```
sudo apt install systemd-resolved -y
```

### Manage networkd and resolved
```
sudo systemctl unmask systemd-networkd.service;
sudo systemctl unmask systemd-resolved.service;
sudo systemctl enable systemd-networkd.service;
sudo systemctl mask networking;
sudo systemctl enable systemd-resolved.service;
```
### config file
- /etc/netplan/10-config.yaml
```
network:
  version: 2
  renderer: networkd
  ethernets:
    enp0s3:
      addresses:
        - 192.168.0.49/24
      nameservers:
        addresses: [8.8.8.8, 8.8.4.4, 192.168.0.1]
      routes:
        - to: default
          via: 192.168.0.1
```

### removed ifupdown and resolvconf
```
reboot
sudo apt purge ifupdown resolvconf -y
```

## Ref
- [Converting Debian 10/11/12 to Netplan.io](https://pedroagrodrigues.com/posts/Debian_To_Netplan/)
- [How To Manage Ubuntu / Debian Networking using Netplan](https://cloudspinx.com/how-to-manage-ubuntu-debian-networking-using-netplan/)
