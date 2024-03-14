---
title: ubuntu修改镜像源
typora-root-url: ubuntu修改镜像源
tags:
  - ubuntu
  - 镜像源
categories:
  - linux
abbrlink: 39a0e8ef
date: 2024-03-10 16:15:46
cover:
top_img:
---

[ubuntu | 镜像站使用帮助 | 清华大学开源软件镜像站 | Tsinghua Open Source Mirror](https://mirrors.tuna.tsinghua.edu.cn/help/ubuntu/)

[Ubuntu修改源镜像方法（22.04也能用）附带常用源镜像地址-CSDN博客](https://blog.csdn.net/qq_14931165/article/details/126863871)

[ubuntu | 镜像站使用帮助 | 北京外国语大学开源软件镜像站 | BFSU Open Source Mirror](https://mirrors.bfsu.edu.cn/help/ubuntu/)

## 查看ubuntu版本号

```
cat /proc/version
```

## 1.简单方法

使用sed命令进行替换。

这里是北外的源

```bash
sed -i s@/archive.ubuntu.com/@/mirrors.bfsu.edu.cn/@g /etc/apt/sources.list
sed -i s@/security.ubuntu.com/@/mirrors.bfsu.edu.cn/@g /etc/apt/sources.list
apt-get clean
apt-get update
```

这里是阿里云的

```bash
sed -i s@/archive.ubuntu.com/@/mirrors.aliyun.com/@g /etc/apt/sources.list
sed -i s@/security.ubuntu.com/@/mirrors.aliyun.com/@g /etc/apt/sources.list
apt-get clean
apt-get update
```

## 2.手动修改

Ubuntu 的软件源配置文件是 `/etc/apt/sources.list`。将系统自带的该文件做个备份，将该文件替换为下面内容，即可使用选择的软件源镜像。

这里以ubuntu22.04为例

```bash
# 默认注释了源码镜像以提高 apt update 速度，如有需要可自行取消注释
deb https://mirrors.bfsu.edu.cn/ubuntu/ jammy main restricted universe multiverse
# deb-src https://mirrors.bfsu.edu.cn/ubuntu/ jammy main restricted universe multiverse
deb https://mirrors.bfsu.edu.cn/ubuntu/ jammy-updates main restricted universe multiverse
# deb-src https://mirrors.bfsu.edu.cn/ubuntu/ jammy-updates main restricted universe multiverse
deb https://mirrors.bfsu.edu.cn/ubuntu/ jammy-backports main restricted universe multiverse
# deb-src https://mirrors.bfsu.edu.cn/ubuntu/ jammy-backports main restricted universe multiverse

deb http://security.ubuntu.com/ubuntu/ jammy-security main restricted universe multiverse
# deb-src http://security.ubuntu.com/ubuntu/ jammy-security main restricted universe multiverse

# 预发布软件源，不建议启用
# deb https://mirrors.bfsu.edu.cn/ubuntu/ jammy-proposed main restricted universe multiverse
# # deb-src https://mirrors.bfsu.edu.cn/ubuntu/ jammy-proposed main restricted universe multiverse
```

其余可以查看来修改

[ubuntu | 镜像站使用帮助 | 北京外国语大学开源软件镜像站 | BFSU Open Source Mirror](https://mirrors.bfsu.edu.cn/help/ubuntu/)
