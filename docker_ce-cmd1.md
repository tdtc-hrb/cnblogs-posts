---
title: "docker(ce) command"
description: "Some commonly used commands"
date: 2025-12-31T08:08:08+08:00
---

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
