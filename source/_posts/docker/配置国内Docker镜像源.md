---
title: 配置国内Docker镜像源
typora-root-url: 配置国内Docker镜像源
tags:
  - linux
  - docker
  - 镜像源
categories:
  - docker
abbrlink: 1ac1559c
date: 2024-03-10 16:03:43
cover:
top_img:
---

## 简介

安装好 `Docker` 后，其 `registry server` 是默认指向 `https://hub.docker.com` 的。在国内该hub源访问速度异常慢，尤其是大一点的镜像经常出现timeout。

我们可以通过切换至国内镜像仓库来解决这一问题

## 配置镜像仓库

1.修改配置文件 `/etc/docker/daemon.json`：

```text
sudo vim /etc/docker/daemon.json
```

2.增加或修改以下配置内容：

```text
{
  "registry-mirrors": [
    "https://dockerproxy.com",
    "https://hub-mirror.c.163.com",
    "https://mirror.baidubce.com",
    "https://ccr.ccs.tencentyun.com"
  ]
}
```

3.重启docker，让配置生效

```text
systemctl restart docker
```

4.检查配置是否生效

```text
docker info
```

输出结果中显示 `Registry Mirrors` 为配置文件配置内容，说明配置成功

## Docker hub 镜像源

| 提供商       | 公共镜像地址                                                 |
| ------------ | ------------------------------------------------------------ |
| 网易云       | [http://hub-mirror.c.163.com](https://link.zhihu.com/?target=http%3A//hub-mirror.c.163.com) |
| 百度云       | [http://mirror.baidubce.com](https://link.zhihu.com/?target=http%3A//mirror.baidubce.com) |
| 腾讯云       | [http://ccr.ccs.tencentyun.com](https://link.zhihu.com/?target=http%3A//ccr.ccs.tencentyun.com) |
| Docker Proxy | [http://dockerproxy.com](https://link.zhihu.com/?target=http%3A//dockerproxy.com) |

## 测试镜像源是否有效

```text
docker pull dockerproxy.com/library/nginx:latest
docker pull hub-mirror.c.163.com/library/nginx:latest
docker pull mirror.baidubce.com/library/nginx:latest
docker pull ccr.ccs.tencentyun.com/library/nginx:latest
```
