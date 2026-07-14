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

## 1.1 安装 Docker Desktop

下载最新的 DEB（https://desktop.docker.com/linux/main/amd64/docker-desktop-amd64.deb） 安装包。

使用 apt 进行安装。

```sh
sudo apt update
sudo apt install ./docker-desktop-amd64.deb
```

> 💡 安装过程结束时，apt 会显示错误信息，可忽略。

## 1.2 Docker Engine

Docker Engine 是一种开源容器化技术，用于构建应用程序并将其容器化。Docker Engine 作为 CS 应用程序运行，包含以下组件：

- Docker Daemon（dockerd）：后台守护进程
- Docker CLI（docker）：命令行客户端
- REST API：两者之间的接口

# 2 安装 Docker

## 2.1 安装 Docker Engine

在 Ubuntu 24.04 中使用 apt 安装 Docker Engine。

### 2.1.1 删除旧版本

批量卸载系统中已安装的 Docker 及相关容器组件。

```sh
sudo apt remove $(dpkg --get-selections docker.io docker-compose docker-compose-v2 docker-doc podman-docker containerd runc | cut -f1)
```

> 💡 `apt remove` 命令在删除包的同时会保留配置文件，`apt purge` 命令会将包及其配置文件全部删除，如果你只是暂时不用，想保留配置以后接着用，用 `remove`，如果想彻底清理干净，像从没装过一样，用 `purge`。

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

安装 docker 包最新版本，安装过程中出现确认提示，全部输入 `y` 确定。

```sh
sudo apt install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
```

安装结束之后，可查看 docker engine 的版本来确定安装情况。

```sh
docker version
```

### 2.1.3 使用脚本安装

通过官方  https://get.docker.com 下载安装脚本，安装最新的稳定版本：

```sh
curl -4 -fsSL https://get.docker.com -o get-docker.sh  # 强制采用 IPv4
sudo sh get-docker.sh
```

> 💡 curl 默认双栈，会优先试 IPv6，很多国内网络 IPv6 是"半通"状态——TCP 能建连，但一到 TLS 握手就被中间设备 RST。`get.docker.com` 是有 AAAA 记录的。

安装结束之后，可查看 docker engine 的版本来确定安装情况。

```sh
docker version
```

## 2.2 卸载 Docker Engine

卸载 Docker Engine，CLI，containerd，和 Docker Compose 包：

```sh
sudo apt purge docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin docker-ce-rootless-extras
```

镜像、容器、数据卷和自定义配置文件不会被自动删除，执行如下命令进行删除：

```sh
sudo rm -rf /var/lib/docker
sudo rm -rf /var/lib/containerd
```

清理安装时所添加的 Docker apt 源和 GPG 公钥，目的是完全移除系统对 Docker 官方源的引用。

```sh
sudo rm /etc/apt/sources.list.d/docker.sources
sudo rm /etc/apt/keyrings/docker.asc
```

## 2.3 Docker 服务命令

Docker Engine 安装后，可使用下列命令对服务进行管理。

```sh
systemctl status docker  # 查看 docker 服务状态
systemctl start docker  # 启动 docker 服务
systemctl stop docker  # 停止 docker 服务
systemctl restart docker  # 重启 docker 服务
systemctl enable docker  # 设置开机启动 docker 服务
```

## 2.4 Docker 镜像加速

在 `/etc/docker/` 目录中创建文件 `daemon.json`，并设置镜像仓库地址。

```json
{
    "registry-mirrors": [
        "https://docker.1ms.run",
        "https://docker.m.daocloud.io",
        "https://lispy.org",
        "https://docker-0.unsee.tech",
        "https://docker.xuanyuan.me"
    ]
}
```

重新启动服务。

```sh
sudo systemctl daemon-reload
sudo systemctl restart docker
```

# 3 使用 Docker

## 3.1 Docker 镜像

Docker 镜像相关命令：

```sh
docker images  # 查看所有本地镜像
docker images –q  # 查看所用本地镜像的id
docker search 镜像名称  # 从 Docker Hub 中查找需要的镜像
docker pull 镜像名称  # 从Docker仓库下载镜像到本地，镜像名称格式为“名称:版本号”，如果版本号不指定则是最新的版本，如果不知道镜像版本，可以去 Docker Hub 搜索对应镜像查看
docker rmi 镜像id # 删除指定本地镜像
docker rmi `docker images -q`  # 删除所有本地镜像
docker system df  # 查看镜像/容器/数据卷所占的空间
```



> 💡 `docker search` 命令默认连接的是 Docker Hub 的索引服务（https://index.docker.io），即使配置了镜像加速器，命令仍然会直接访问 Docker Hub 的搜索接口。国内网络环境下，`docker search` 命令搜索镜像可能会因为网络问题而超时。在不使用科学上网的情况下，配置镜像加速器后，直接使用`docker pull`拉取镜像即可，没有必要通过 `docker search` 命令搜索镜像。

## 3.2 Docker 容器

可以把容器看作是一个简易版的 Linux 环境（包括 root 用户权限、进程空间、用户空间和网络空间等）和运行在其中的应用程序。

Docker 容器相关命令：

```sh
docker run  # 新建并启动容器
docker ps  # 列出所有正在运行的容器
exit 或 ctrl + p + q  # 退出容器
docker start  # 启动已停止运行的容器
docker restart  # 重启容器
docker stop  # 停止容器
docker kill  # 强制停止容器
docker rm  # 删除容器
docker logs  # 查看容器日志
docker top  # 查看容器内运行的进程
docker inspect  # 查看容器内部细节
docker exec  # 进入正在运行的容器
docker cp  # 拷贝容器文件到主机
docker export  # 导出容器
docker import  # 导入容器
```



# 4 服务编排

使用Docker Compose，可以用一个 YAML 文件定义一组要启动的容器，以及容器运行时的属性，Docker Compose 称这些容器为“服务”。

## 4.1 安装 Docker Compose

安装过程见 [2.1.2 使用 apt 安装](#2.1.2 使用 apt 安装)。

查看 Docker Compose 版本。

```sh
docker compose version
```

## 4.2 使用 Docker Compose

Compose 使用的三个步骤：

- 使用 Dockerfile 定义应用程序的环境。
- 使用 docker-compose.yml 定义构成应用程序的服务，这样它们可以在隔离环境中一起运行。rm
- 最后，执行 docker-compose up 命令来启动并运行整个应用程序。

```sh
sudo docker compose up -d  # 使用守护进程模式启动
sudo docker compose ps  # 查看服务运行状态
sudo docker compose logs  # 显示服务的日志
sudo docker compose stop  # 停止正在运行的服务
sudo docker compose rm  # 删除服务
```

> Docker Compose V1 是独立由 Python 写的二进制，已被标记为 deprecated（废弃），2023 年起官方不再维护，其命令格式为 `docker-compose`。Docker Compose V2 作为 Docker CLI 的插件分发，使用 Go 重写，当前主流默认，Docker Desktop 自带，其命令格式为 `docker compose`。
