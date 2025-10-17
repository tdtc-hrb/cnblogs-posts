---
title: "docker(ce) on Ubuntu"
description: "Docker and Docker compose"
date: 2020-10-13T08:08:08+08:00
---
> Note:
>> ```bash
>> # - requires given linux commands to be executed with root privileges either directly as a root user or by use of sudo command
>> $ - requires given linux commands to be executed as a regular non-privileged user
>> ```

# Docker CE
## 1. install
```bash
$sudo apt install docker.io
```

## 2. enable
```bash
$ sudo systemctl start docker
$ sudo systemctl enable docker
```

test:
```bash
$ docker --version
```

# Docker compose
## 1. down
```bash
sudo curl -L "https://github.com/docker/compose/releases/download/1.29.2/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
```

## 2. exec
```bash
sudo chmod +x /usr/local/bin/docker-compose
```

test:
```bash
tdtc@tdtc306:~$ docker-compose --version
docker-compose version 1.29.2, build xxx
```

# other command
## 1. 清理image
```bash
sudo docker system prune -a
```

## 2. 管理image
### 1) ps
```bash
sudo dokcer ps
```

### 2) 进入container
fat shell:
```bash
sudo docker exec -it <container-id> bash
```

simple shell:
```bash
sudo docker exec -it <container-id> sh
```

### 3)container内执行
查看:
```bash
cat Gopkg.toml |more
```
编辑:
```bash
apt-get update
apt-get install vim -y
vi Gopkg.toml
```

### 4) stop all containers
```bash
sudo docker stop $(sudo docker ps -q)
```

# Reference
- [How to Install Docker On Ubuntu 18.04 Bionic Beaver](https://linuxconfig.org/how-to-install-docker-on-ubuntu-18-04-bionic-beaver)
- [Install Docker Compose](https://docs.docker.com/compose/install/)(Offical)
- [How do I get into a Docker container's shell?](https://stackoverflow.com/questions/30172605/how-do-i-get-into-a-docker-containers-shell)
