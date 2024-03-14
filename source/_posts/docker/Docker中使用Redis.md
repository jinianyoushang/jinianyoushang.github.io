---
title: Docker中使用Redis
typora-root-url: Docker中使用Redis
tags:
  - redis
categories:
  - docker
abbrlink: 175acaf3
date: 2024-03-11 10:03:51
cover:
top_img:
---

# 在Docker中使用Redis

### 1. 简介

本文章将介绍如何使用 Docker 探索 Redis。我们可以在 Docker for Windows 、Docker for mac 或者 Linux 模式下运行 Docker 命令。

>  本文是基于Docker for mac。 

### 2. 查看可用的 Redis 版本

可以在[镜像仓库](https://cloud.tencent.com/product/tcr?from=10680)中查看 [Redis](https://hub.docker.com/_/redis?tab=tags) 镜像：

![img](1620.png)

### 3. 获取镜像

使用如下命令拉取官方最新版本的镜像：

```javascript
docker pull redis:latest
```

![img](1620-16645387358191.png)

### 4. 查看本地镜像

使用如下命令来查看是否已安装了Redis镜像：

```javascript
docker images
```

![img](1620-16645387358192.png)

在图中我们可以看到我们已经安装了最新版本（latest）的 Redis 镜像。

### 5. 运行[容器](https://cloud.tencent.com/product/tke?from=10680)

我们给容器起一个名字 `docker-redis`，同时公开 6379 端口（Redis 默认值），使用如下命令运行容器:

```javascript
docker run -d -p 6379:6379 --name docker-redis redis
```



![img](1620-16645387358193.png)

>  -p 6379:6379：映射容器服务的 6379 端口到[宿主机](https://cloud.tencent.com/product/cdh?from=10680)的 6379 端口。外部可以直接通过宿主机ip:6379 访问到 Redis 的服务。 

可以通过如下命令查看容器的运行信息来判断容器是否运行成功：

```javascript
docker ps
```



![img](1620-16645387358204.png)

还可以通过如下命令查看日志输出：

```javascript
docker logs docker-redis
```

![img](1620-16645387358205.png)

### 6. 在容器中运行Redis CLI

接着我们通过在容器中运行 redis-cli 来连接 redis 服务。我们将在运行中的容器中用 `-it` 选项来启动一个新的交互式会话，并使用它来运行 `redis-cli`：

```javascript
docker exec -it docker-redis /bin/bash
```

![img](1620-16645387358206.png)

我们已经连接到容器，现在让我们运行 `redis-cli`：

```javascript
root@517350f4f2bb:/data# redis-cli
```

现在我们可以运行一些基本的 Redis 命令：

![img](1620-16645387358207.png)

### 7. 清理容器

让我们停止 `docker-redis` 容器并删除：

```javascript
docker stop docker-redis
docker rm docker-redis
```
