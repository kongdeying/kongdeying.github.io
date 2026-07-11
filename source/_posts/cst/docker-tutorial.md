---
title: Docker 学习指南
date: 2026-07-11 14:48:08
categories:
  - 计算机
tags:
  - Docker
---

（连载中）

# 1 关于 Docker

Docker 是一个开源的应用容器引擎，基于 Go 语言，并遵从 Apache2.0 协议开源。

Docker 从 17.03 版本之后分为 CE（Community Edition: 社区版）和 EE（Enterprise Edition: 企业版）。社区版适合开发者和小型团队，可以快速上手，并获得社区的支持。企业版则更适合于需要严格控制和保护的企业环境，提供了更多的安全性和技术支持。学习和小规模场景使用社区版就可以了。

## 1.1 Docker Engine

Docker Engine 是一种开源容器化技术，用于构建应用程序并将其容器化。Docker Engine 作为 CS 应用程序运行，包含以下组件：

- Docker Daemon（dockerd）：后台守护进程
- Docker CLI（docker）：命令行客户端
- REST API：两者之间的接口



# 2 安装 Docker

## 2.1 安装 Docker Engine

在 Ubuntu 24.04 中使用 apt 安装 Docker Engine。

### 2.1.1 删除旧版本：

```sh
sudo apt remove $(dpkg --get-selections docker.io docker-compose docker-compose-v2 docker-doc podman-docker containerd runc | cut -f1)
```

### 2.1.2 使用 apt 安装

设置 docker apt 仓库

```sh
# Add Docker's official GPG key:
sudo apt update
sudo apt install ca-certificates curl
sudo install -m 0755 -d /etc/apt/keyrings
sudo curl -fsSL https://download.docker.com/linux/ubuntu/gpg -o /etc/apt/keyrings/docker.asc
sudo chmod a+r /etc/apt/keyrings/docker.asc

# Add the repository to Apt sources:
sudo tee /etc/apt/sources.list.d/docker.sources <<EOF
Types: deb
URIs: https://download.docker.com/linux/ubuntu
Suites: $(. /etc/os-release && echo "${UBUNTU_CODENAME:-$VERSION_CODENAME}")
Components: stable
Architectures: $(dpkg --print-architecture)
Signed-By: /etc/apt/keyrings/docker.asc
EOF

sudo apt update
```

安装最新版本

```sh
sudo apt install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
```

### 2.1.3 使用脚本安装

```sh
curl -fsSL https://test.docker.com -o test-docker.sh
sudo sh test-docker.sh
```



## 2.2 Docker 服务命令

```sh
systemctl status docker  # 查看 docker 服务状态
systemctl start docker  # 启动 docker 服务
systemctl stop docker  # 停止 docker 服务
systemctl restart docker  # 重启 docker 服务
systemctl enable docker  # 设置开机启动 docker 服务
```



# 3 使用 Docker



# 4 服务编排

