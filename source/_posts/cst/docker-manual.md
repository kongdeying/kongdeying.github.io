---
title: Docker 操作手册
date: 2026-07-15 10:45:26
categories:
  - 计算机
tags: Docker
---

# 1 镜像加速服务

国内从 Docker Hub 拉取镜像有时会遇到困难，此时可以配置镜像加速器。

**方式一：直接使用**

直接使用镜像域名拼接官方镜像名，例如要拉取镜像 `istio/distroless`，可以使用：

```bash
docker pull 镜像域名/istio/distroless
```

**方式二：长久配置**

修改配置文件 `/etc/docker/daemon.json`（如果不存在则需要创建）：

1. 创建配置目录

```bash
sudo mkdir -p /etc/docker/
```

2. 写入配置

```bash
sudo tee /etc/docker/daemon.json <<-'EOF'
{
    "registry-mirrors": [
        "https://docker-0.unsee.tech"
    ]
}
EOF
```

3. 重启docker服务

```bash
sudo systemctl daemon-reload && sudo systemctl restart docker
```

> 💡 2020 年 8 月，Docker 更新[服务条款](https://www.docker.com/legal/docker-terms-use/)，禁止美国"[实体清单](https://www.ecfr.gov/current/title-15/subtitle-B/chapter-VII/subchapter-C/part-744#Supplement-No.-4-to-Part-744)"上的实体访问其商业服务。
>
> 2023 年 5 月，`hub.docker.com` 网站在国内开始无法正常访问，但 docker pull 还能走镜像加速。
>
> 2024 年 6 月 7 日，GFW 对 `docker.com`及其相关域名做 DNS 污染 + SNI 阻断；同期国内多家公益镜像站（中科大、上交、阿里云加速、腾讯云加速等）接"上级通知"关停 Docker Hub 镜像缓存服务。
>
> 2025 年 4 月，Docker Hub 自身又加了速率限制（未登录每 IP 每小时 10 次）。
