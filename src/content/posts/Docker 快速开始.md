---
title: Docker 快速开始
published: 2025-09-02
description: "本文介绍Docker的安装,配置和简单使用"
image: ""
tags: [Docker, linux]
category: "教程"
draft: false
# lang: ''
---

:::tip
若执行失败,请使用管理员权限
:::

## 安装 Docker

使用清华源进行安装

以下摘除自[清华源使用帮助](https://mirrors.tuna.tsinghua.edu.cn/help/docker-ce/)

删除存在的 docker:

```bash
for pkg in docker.io docker-doc docker-compose podman-docker containerd runc; do apt-get remove $pkg; done
```

安装依赖:

```bash
apt-get update
apt-get install ca-certificates curl gnupg
```

信任 GPG 公钥,添加仓库(Debian):

```bash
install -m 0755 -d /etc/apt/keyrings
curl -fsSL https://download.docker.com/linux/debian/gpg | gpg --dearmor -o /etc/apt/keyrings/docker.gpg
sudo chmod a+r /etc/apt/keyrings/docker.gpg
echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.gpg] https://mirrors.tuna.tsinghua.edu.cn/docker-ce/linux/debian \
  "$(. /etc/os-release && echo "$VERSION_CODENAME")" stable" | \
  tee /etc/apt/sources.list.d/docker.list > /dev/null
```

执行安装:

```bash
apt-get update
apt-get install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
```

## 设置 docker 用户组

```bash
# 没有的话,添加docker用户组
# sudo groupadd docker
sudo usermod -aG docker $USER # 添加用户至docker组
newgrp docker #更新用户组
```

## 设置 Docker Hub 源

```bash
# 创建目录
sudo mkdir -p /etc/docker
# 写入镜像配置
sudo tee /etc/docker/daemon.json <<-'EOF'
{
    "registry-mirrors": [
        "https://docker.m.daocloud.io",
        "https://dockerproxy.com",
        "https://docker.mirrors.ustc.edu.cn",
        "https://docker.nju.edu.cn"
    ]
}
EOF
# 重启docker服务
sudo systemctl daemon-reload
sudo systemctl restart docker
```

查看镜像:

```bash
docker info | grep "Registry Mirrors"
```

## 使用 Docker

Hello World

```bash
docker run hello-world
```

列出所用镜像

```bash
docker images
```
