---
title: docker在各个平台的安装
typora-root-url: docker在各个平台的安装
tags:
  - docker
  - linux
  - centos
  - ubuntu
categories:
  - docker
abbrlink: 11c41fc3
date: 2024-03-10 16:07:17
cover:
top_img:
---

### 安装docker

#### 官网

[Home - Docker](https://www.docker.com/)

#### windows安装

[Windows Docker 安装 | 菜鸟教程 (runoob.com)](https://www.runoob.com/docker/windows-docker-install.html)

#### centos安装

[CentOS Docker 安装 | 菜鸟教程 (runoob.com)](https://www.runoob.com/docker/centos-docker-install.html)

[Install Docker Engine on CentOS | Docker Documentation](https://docs.docker.com/engine/install/centos/)

```sh
#旧机器 Uninstall old versions
sudo yum remove docker \
                  docker-client \
                  docker-client-latest \
                  docker-common \
                  docker-latest 
                  docker-latest-logrotate \
                  docker-logrotate \
                  docker-engine
              
#新机器 Set up the repository
sudo yum install -y yum-utils
#yum-config-manager --add-repo https://download.docker.com/linux/centos/docker-ce.repo
yum-config-manager --add-repo http://mirrors.aliyun.com/docker-ce/linux/centos/docker-ce.repo

#重建yum缓冲
yum makecache fast

#安装 DOCKER CE
yum -y install docker-ce docker-ce-cli containerd.io

#启动 docker
systemctl start docker

#测试
docker version
docker run hello-world

#卸载
systemctl stop docker
yum remove docker-ce docker-ce-cli containerd.io
rm -rf /var/lib/docker
rm -rf /var/lib/containerd
```

#### ubuntu安装

本文以Ubuntu20.05系统为例安装docker，[Ubuntu官方下载地址](https://link.zhihu.com/?target=https%3A//ubuntu.com/download)。

检查卸载老版本docker

ubuntu下自带了docker的库，不需要添加新的源。
但是ubuntu自带的docker版本太低，需要先卸载旧的再安装新的。

注：docker的旧版本不一定被称为docker，[http://docker.io](https://link.zhihu.com/?target=http%3A//docker.io) 或 docker-engine也有可能，所以我们卸载的命令为：

```bash
$ apt-get remove docker docker-engine docker.io containerd runc
```

如果不能正常卸载，出现如下情况，显示无权限时，需要添加管理员权限才可进行卸载：



![img](v2-90241efe0f829a37c1db3fad3349c849_720w.webp)


我们就需要使用`sudo apt-get remove docker docker-engine docker.io containerd runc`命令使用root权限来进行卸载。

安装步骤

### 更新软件包

在终端中执行以下命令来更新Ubuntu软件包列表和已安装软件的版本:

```text
sudo apt update sudo apt upgrade
```

### 安装docker依赖

Docker在Ubuntu上依赖一些软件包。执行以下命令来安装这些依赖:

```text
apt-get install ca-certificates curl gnupg lsb-release
```

### 添加Docker官方GPG密钥

执行以下命令来添加Docker官方的GPG密钥:

```text
curl -fsSL http://mirrors.aliyun.com/docker-ce/linux/ubuntu/gpg | sudo apt-key add -
```

结果如下：

![img](v2-0bd38706177023cfe438258e1009d83a_720w.webp)



### 添加Docker软件源

执行以下命令来添加Docker的软件源:

```text
sudo add-apt-repository "deb [arch=amd64] http://mirrors.aliyun.com/docker-ce/linux/ubuntu $(lsb_release -cs) stable"
```

**注：该命令需要使用root权限**



![img](v2-cbcdc65224f69f4e6c3c5ee5ac696012_720w.webp)

### 安装docker

执行以下命令来安装Docker:

```text
apt-get install docker-ce docker-ce-cli containerd.io
```

### 配置用户组（可选）

默认情况下，只有root用户和docker组的用户才能运行Docker命令。我们可以将当前用户添加到docker组，以避免每次使用Docker时都需要使用sudo。命令如下：

```text
sudo usermod -aG docker $USER
```

**注：重新登录才能使更改生效。**

运行docker

我们可以通过启动`docker`来验证我们是否成功安装。命令如下：

```text
systemctl start docker
```

### **安装工具**

```text
apt-get -y install apt-transport-https ca-certificates curl software-properties-common
```

### **重启docker**

```text
service docker restart
```

### **验证是否成功**

```text
sudo docker run hello-world
```

运行命令后，结果如下：



![img](v2-97314d2165190bb4a6926facc7e77eeb_720w.webp)



因为我们之前没有拉取过`hello-world`，所以运行命令后会出现本地没有该镜像，并且会自动拉取的操作。

### **查看版本**

我们可以通过下面的命令来查看`docker`的版本

```text
sudo docker version
```

结果如下：

![img](v2-89ef2e517ffd86d80f65b6a0d2a0b159_720w.webp)



### **查看镜像**

上面我们拉取了hello-world的镜像，现在我们可以通过命令来查看镜像，命令如下：

```text
sudo docker images
```

结果如下图：

![img](v2-240135177c5b1fbe9d8897dac4beec4c_720w.webp)

出现上述情况，即表示我们成功在Ubuntu系统上安装了docker。
