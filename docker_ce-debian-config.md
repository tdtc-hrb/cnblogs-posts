---
title: "Install Docker on Debian"
description: "Docker Engine and Docker compose"
date: 2025-12-31T08:08:08+08:00
---
Note:
```bash
# - requires given linux commands to be executed with root privileges either directly as a root user or by use of sudo command
$ - requires given linux commands to be executed as a regular non-privileged user
```

# Docker Engine
```
# Add Docker's official GPG key:
sudo apt update
sudo apt install ca-certificates curl
sudo install -m 0755 -d /etc/apt/keyrings
sudo curl -fsSL https://download.docker.com/linux/debian/gpg -o /etc/apt/keyrings/docker.asc
sudo chmod a+r /etc/apt/keyrings/docker.asc

# Add the repository to Apt sources:
sudo tee /etc/apt/sources.list.d/docker.sources <<EOF
Types: deb
URIs: https://download.docker.com/linux/debian
Suites: $(. /etc/os-release && echo "$VERSION_CODENAME")
Components: stable
Signed-By: /etc/apt/keyrings/docker.asc
EOF

sudo apt update
```
## 1. install
```bash
$sudo apt install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
```

### docker source
```
sudo systemctl daemon-reload
```
#### [源地址](https://gitee.com/xiaobin80/grpc-hello)
- /etc/docker/daemon.json
```
{
  "registry-mirrors" : [
    "https://registry.docker-cn.com",
    "https://mirror.ccs.tencentyun.com"
  ]
}
```
每次都要更新[China源](https://zhuanlan.zhihu.com/p/1926211424398603825)

## 2. enable
```bash
$ sudo systemctl start docker
$ sudo systemctl status docker
```
### test
```bash
$ docker --version
$ sudo docker run hello-world
```
#### output info
```
Unable to find image 'hello-world:latest' locally
latest: Pulling from library/hello-world
17eec7bbc9d7: Pull complete
ea52d2000f90: Download complete
Digest: sha256:d4aaab6242e0cace87e2ec17a2ed3d779d18fbfd03042ea58f2995626396a274
Status: Downloaded newer image for hello-world:latest

Hello from Docker!
This message shows that your installation appears to be working correctly.
```

# Docker compose
Install the Docker Compose standalone (Legacy)
## 1. down
```bash
sudo curl -SL https://github.com/docker/compose/releases/download/v5.0.1/docker-compose-linux-x86_64 -o /usr/local/bin/docker-compose
```

## 2. exec
```bash
sudo chmod +x /usr/local/bin/docker-compose
```
ou can also create a symbolic link to /usr/bin or any other directory in your path:
```
sudo ln -s /usr/local/bin/docker-compose /usr/bin/docker-compose
```
### test
```bash
tdtc@tdtc306:~$ docker-compose --version
Docker Compose version v5.0.1
```

# Reference
- [Install Docker Compose](https://docs.docker.com/compose/install/standalone#on-linux)
- [Install using the apt repository](https://docs.docker.com/engine/install/debian/#install-using-the-repository)
- [docker - ubuntu](https://tdtc-hrb.github.io/cnblogs/post/docker_ce-ubuntu-config/)
