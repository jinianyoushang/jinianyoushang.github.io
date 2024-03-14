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

推荐使用阿里云自己注册的

[配置docker加速服务 | xd's blog](https://jinianyoushang.github.io/posts/4c1a7ee4/)

| 提供商           | 公共镜像地址                                                 |
| ---------------- | ------------------------------------------------------------ |
| 网易云           | [http://hub-mirror.c.163.com](https://link.zhihu.com/?target=http%3A//hub-mirror.c.163.com) |
| 百度云           | [http://mirror.baidubce.com](https://link.zhihu.com/?target=http%3A//mirror.baidubce.com) |
| 腾讯云           | [http://ccr.ccs.tencentyun.com](https://link.zhihu.com/?target=http%3A//ccr.ccs.tencentyun.com) |
| Docker Proxy     | [http://dockerproxy.com](https://link.zhihu.com/?target=http%3A//dockerproxy.com) |
| Docker中国区官方 | https://registry.docker-cn.com                               |
|                  |                                                              |

## 测试镜像源是否有效

```text
docker pull dockerproxy.com/library/nginx:latest
docker pull hub-mirror.c.163.com/library/nginx:latest
docker pull mirror.baidubce.com/library/nginx:latest
docker pull ccr.ccs.tencentyun.com/library/nginx:latest
```

## 源镜像测速

### Linux

在Linux下面有`time`命令，可以使用该命令对源进行测速：

```bash
time docker pull nginx:latest
```

测速结果大致如下：

```bash
real   1m14.078s
user   0m0.176s
sys    0m0.120s
```

### Windows

在Windows的PowerShell下面可以使用以下命令测速：

```powershell
Measure-Command {docker pull nginx:latest | Out-Default}
```

测速结果大致如下：

```powershell
Days              : 0
Hours             : 0
Minutes           : 0
Seconds           : 4
Milliseconds      : 217
Ticks             : 42174202
TotalDays         : 4.88127337962963E-05
TotalHours        : 0.00117150561111111
TotalMinutes      : 0.0702903366666667
TotalSeconds      : 4.2174202
TotalMilliseconds : 4217.4202
```

## 参考资料

[Docker Hub 镜像源 - 掘金 (juejin.cn)](https://juejin.cn/post/7165806699461378085)
