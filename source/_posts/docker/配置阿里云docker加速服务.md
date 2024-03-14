---
title: 配置阿里云docker加速服务
typora-root-url: 配置阿里云docker加速服务
tags:
  - 镜像加速
categories:
  - docker
abbrlink: 4c1a7ee4
date: 2024-03-11 09:57:07
cover:
top_img:
---

# 配置阿里云docker加速服务

**简介：** 加速器推荐• 阿里云的加速器https://help.aliyun.com/document_detail/60750.html• DaoCloud 加速器：https://www.daocloud.io/mirror#accelerator-doc1. 安装／升级 Docker 客户端推荐安装 1.10.0 以上版本的 Docker 客户端，参考文档 docker-ce2. 配置镜像加速器直接登录阿里云 - 容器 Hub 服务控制台后，点击“镜像加速器”标签，也会出现相应信息。在出现的“容器镜像服务”页面，依次查找：镜像中心---》镜像加速器，并点击，可以得到一个专属的镜像加速

## 加速器推荐

- 阿里云的加速器[https://help.aliyun.com/document_detail/60750.html](https://links.jianshu.com/go?spm=a2c6h.12873639.article-detail.7.51082fb4TLZoQh&to=https%3A%2F%2Fhelp.aliyun.com%2Fdocument_detail%2F60750.html)

## 1. 安装／升级 Docker 客户端

推荐安装 1.10.0 以上版本的 Docker 客户端，参考文档 [docker-ce](https://links.jianshu.com/go?to=https%3A%2F%2Fyq.aliyun.com%2Farticles%2F110806)

## 2. 配置镜像加速器

直接登录[阿里云 - 容器 Hub 服务控制台](https://links.jianshu.com/go?to=https%3A%2F%2Fcr.console.aliyun.com%2F)后，点击“镜像加速器”标签，也会出现相应信息。

在出现的“容器镜像服务”页面，依次查找：镜像中心---》镜像加速器，并点击，可以得到一个专属的镜像加速地址，类似于“[https://d4iq1rp9.mirror.aliyuncs.com](https://links.jianshu.com/go?to=https%3A%2F%2F1234abcd.mirror.aliyuncs.com%2F)”。

![image.png](214d2b30d2cc42488f224e83b1bb2294.png)

根据页面中的“操作文档”信息，对应系统类型，配置自己的 Docker 镜像加速器。

例如：CentOS系统

针对Docker客户端版本大于 1.10.0 的用户

您可以通过修改daemon配置文件 /etc/docker/daemon.json 来使用加速器

```shell
sudo mkdir -p /etc/docker
sudo tee /etc/docker/daemon.json <<-'EOF'
{
  "registry-mirrors": ["https://xxxxxx.mirror.aliyuncs.com"]
}
EOF
```

最后别忘记重新启动 docker：

```shell
sudo systemctl daemon-reload
sudo systemctl restart docker
```

## 不需要登录账号（支持匿名pull）

国内从 DockerHub 拉取镜像有时会遇到困难，此时可以配置镜像加速器。Docker 官方和国内很多云服务商都提供了国内加速器服务，例如：

Docker 官方提供的中国镜像库：[https://registry.docker-cn.com](https://links.jianshu.com/go?to=https%3A%2F%2Fregistry.docker-cn.com)

七牛云加速器：[https://reg-mirror.qiniu.com](https://links.jianshu.com/go?to=https%3A%2F%2Freg-mirror.qiniu.com)

对于使用 systemd 的系统(Ubuntu 16.04+、Debian 8+、CentOS 7+)，可以创建 /etc/docker/daemon.json 文件，并写入如下内容（如果文件不存在请新建该文件）：

```json
{
  "registry-mirrors": [
    "https://hub-mirror.c.163.com/"
  ]
}
```

最后别忘记重新启动 docker：

```
sudo systemctl daemon-reload
sudo systemctl restart docker
```

## 参考

[配置 docker 加速服务-阿里云开发者社区 (aliyun.com)](https://developer.aliyun.com/article/929177#:~:text=安装／升级 Docker 客户端推荐安装 1.10.0 以上版本的 Docker 客户端，参考文档,docker-ce2. 配置镜像加速器直接登录阿里云 - 容器 Hub 服务控制台后，点击“镜像加速器”标签，也会出现相应信息。 在出现的“容器镜像服务”页面，依次查找：镜像中心---》镜像加速器，并点击，可以得到一个专属的镜像加速)
