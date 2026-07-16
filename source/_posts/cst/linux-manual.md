---
title: Linux 操作手册
date: 2026-07-15 17:50:57
categories:
  - 计算机
tags:
---

# 1 在 Ubuntu 24.04 上安装 SSH

1.安装 OpenSSH 服务器

```sh
sudo apt update  # 更新软件包列表
sudo apt install ssh
```

安装后，SSH 服务将自动启动。

2.启用 SSH 服务

```sh
sudo systemctl status ssh  # 查看 SSH 服务状态
sudo systemctl enable ssh  # 设置 SSH 服务在服务器重启后启动
sudo systemctl start ssh   # 若 SSH 服务未启动，使用该命令启动服务
```

3.配置防火墙

Ubuntu 24.04 安装后，默认 UFW 防火墙处于未启用状态，建议在受保护的网络环境中启用防火墙。

```sh
sudo ufw status     # 查看防火期状态
sudo ufw allow ssh  # 允许入站 SSH 连接
sudo ufw enable     # 启用防火墙
```

4.配置 SSH 服务器

为了确保服务器的安全并为远程访问提供便利，可对 SSH 服务器进行一些配置和安全性设置。

- 修改 SSH 默认端口

默认情况下，SSH 服务器的端口是 22，为提高安全性，可将 SSH 端口修改为其他非标准端口。在 `/etc/ssh/sshd_config` 配置文件中，修改 `Port` 为其他端口号。

修改后重新加载 SSH 服务。

```sh
sudo systemctl reload ssh
```

- 禁止 root 登录

禁止 root 通过 SSH 登录是一种提高安全性的方法。

修改配置文件，并重新加载 SSH 服务。

```sh
PermitRootLogin no
```

- 使用 SSH 密钥认证

SSH密钥认证比基于密码的认证更安全。首先，生成SSH密钥对：

```sh
ssh-keygen -t rsa
```

按照提示完成密钥生成过程。然后将公钥复制到服务器上：

```sh
ssh-copy-id username@server_ip_address
```

输入密码，将公钥添加到服务器的授权密钥列表中。现在您可以使用密钥对进行SSH登录，而无需密码。

- 安装 Fail2ban

Fail2ban是一个防止恶意登录尝试的工具，可以自动禁止IP地址。安装Fail2ban：

```sh
sudo apt install fail2ban
```

- 启用登录审计

启用登录审计功能可以帮助您监控谁何时登录了服务器。您可以查看 `/var/log/auth.log` 文件来查看登录活动。

# 2 ifconfig 命令

安装 Ubuntu 24.04 后，默认情况下无法使用 ifconfig 命令，需要安装 net-tools 工具。

```sh
sudo apt update
sudo apt install net-tools
```

ifconfig（network interfaces configuring）用于显示或配置网络设备的参数信息。

```sh
ifconfig  # 显示激活状态（up 状态）的网卡信息
```

以 eth0 为例，其网卡信息含义如下所示：

| 名称         | 含义                                                         |
| ------------ | ------------------------------------------------------------ |
| eth0         | 网卡名称                                                     |
| Link encap   | 网卡连接类型                                                 |
| HWaddr       | 网卡 MAC 地址                                                |
| inet addr    | IPv4 的 IP 地址                                              |
| Bcast        | 广播地址                                                     |
| Mask         | 子网掩码 |
| inet6 addr   | IPv6 的 IP 地址                                              |
| Scope        | IPv6 的域范围                                                |
| UP           | 表示网卡已经启用                                             |
| BROADCAST    | 表示主机支持广播                                             |
| RUNNING      | 表示网卡正在运行中                                           |
| MULTICAST    | 表示主机支持多播                                             |
| MTU          | 最大传输单元 |
| Metric       | 表示接口度量值                                               |
| RX  packets: | 接收的数据包数                                               |
| errors:      | 接收时错误的数据包数                                         |
| dropped      | 接收时丢弃的数据包数                                         |
| overruns:    | 接收时由于 buffer 溢出而丢弃的数据包数                       |
| frame:       | 接收时由于 frame 错位而丢弃的数据包数                        |
| TX packets   | 发送的数据包数                                               |
| errors:      | 发送时错误的数据包数                                         |
| dropped:     | 发送时丢弃的数据包数 |
| overruns:    | 发送时由于 buffer 溢出而丢弃的数据包数                       |
| carrier:     | 发送时由于 carrier 错误而丢弃的数据包数                      |
| collisions:  | 表示冲突信息的数据包数目                                     |
| txqueuelen:  | 表示网卡设置传输队列的大小                                   |
| RX bytes:    | 接收的数据包字节数|
| TX bytes:    | 发送的数据包字节数                                           |
