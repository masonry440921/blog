---
title: docker uninstall and install
published: 2025-01-08
description: This post demonstrates how to include embedded video in a blog post.
tags: [docker]
category: Linux
draft: false
---

Just copy the embed code from YouTube or other platforms, and paste it in the markdown file.

```yaml
---
title: docker 
published: 2025-01-08

---

```

### 删除docker及安装时自动安装的所有包
```
apt-get autoremove docker docker-ce docker-engine  docker.io  containerd runc

```

### 查看docker是否卸载干净
```
dpkg -l | grep docker

 dpkg -l |grep ^rc|awk '{print $2}' |sudo xargs dpkg -P # 删除无用的相关的配置文件

```

### 删除docker的相关配置&目录
```
apt-get autoremove docker-ce-*

```

### 确定docker卸载完毕
```
docker --version

```

### Install using the apt repository
Before you install Docker Engine for the first time on a new host machine, you need to set up the Docker apt repository. Afterward, you can install and update Docker from the repository.
```
# Add Docker's official GPG key:
sudo apt-get update
sudo apt-get install ca-certificates curl
sudo install -m 0755 -d /etc/apt/keyrings
sudo curl -fsSL https://download.docker.com/linux/ubuntu/gpg -o /etc/apt/keyrings/docker.asc
sudo chmod a+r /etc/apt/keyrings/docker.asc

# Add the repository to Apt sources:
echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.asc] https://download.docker.com/linux/ubuntu \
  $(. /etc/os-release && echo "$VERSION_CODENAME") stable" | \
  sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
sudo apt-get update

```
### nstall the Docker packages.
```
sudo apt-get install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin

sudo docker run hello-world
```

### docker 常用命令
---
docker load -i xx.tar #导入镜像

docker images

docker ps -a
docker ps

docker run image

docker stop 容器id
docker start 容器id
docker restart 容器id

docker logs 容器id

docker exec -it 容器id /bin/bash

docker cp 容器id:/filepath /linux/filepath

docker cp /linux/filepath 容器id:/filepath

docker-compose up -d #docker-compose.yml 里面填pull好的镜像id
---


