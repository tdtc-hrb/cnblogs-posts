---
title: "install go on ubuntu"
description: "go v11+"
date: 2020-05-17T00:08:08+08:00
---
> WSL: Ubuntu 18.04

# 1. install go
[go1.13.10](https://dl.google.com/go/go1.13.10.linux-amd64.tar.gz)

## 1) unzip
rename:
```bash
cd /usr/local/go
sudo mv go 1.13
```

## 2) set path

```bash
vi ~/.bashrc
```

```bash
export GOROOT=/usr/local/go/1.13

export GOPATH=/home/tdtc/go

PATH=$PATH:$GOROOT/bin:$GOPATH/bin
```

```bash
source ~/.bashrc
```

# test
```bash
go version
```

# Reference
- [How To Install Go 1.12 on Ubuntu 18.04 & 16.04 LTS](https://tecadmin.net/install-go-on-ubuntu/)
