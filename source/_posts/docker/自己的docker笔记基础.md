---
title: 自己的docker笔记基础
typora-root-url: 自己的docker笔记基础
tags:
  - docker
  - redis
  - 镜像
  - mysql
categories:
  - docker
abbrlink: 19b4cc0f
date: 2024-03-11 10:03:33
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

[最详细的ubuntu安装docker教程 | xd's blog (jinianyoushang.github.io)](https://jinianyoushang.github.io/posts/3756cf19/)

#### 设置镜像加速

##### centos/ubuntu

```shell
#我自己的网址 https://******.mirror.aliyuncs.com

sudo mkdir -p /etc/docker
sudo tee /etc/docker/daemon.json <<-'EOF'
{
  "registry-mirrors": ["https://******.mirror.aliyuncs.com"]
}
EOF
sudo systemctl daemon-reload
sudo systemctl restart docker
```

##### windows

针对安装了Docker Toolbox的用户，您可以参考以下配置步骤：

创建一台安装有Docker环境的Linux虚拟机，指定机器名称为default，同时配置Docker加速器地址。

```
docker-machine create --engine-registry-mirror=https://******.mirror.aliyuncs.com -d virtualbox default
```

查看机器的环境配置，并配置到本地，并通过Docker客户端访问Docker服务。

```
docker-machine env defaulteval "$(docker-machine env default)"docker info
```

针对安装了Docker for Windows的用户，您可以参考以下配置步骤：

在系统右下角托盘图标内右键菜单选择 Settings，打开配置窗口后左侧导航菜单选择 Docker Daemon。编辑窗口内的JSON串，填写下方加速器地址：

```
{
  "registry-mirrors": ["https://******.mirror.aliyuncs.com"]
}
```

编辑完成后点击 Apply 保存按钮，等待Docker重启并应用配置的镜像加速器。

##### 注意

Docker for Windows 和 Docker Toolbox互不兼容，如果同时安装两者的话，需要使用hyperv的参数启动。

```
docker-machine create --engine-registry-mirror=https://******.mirror.aliyuncs.com -d hyperv default
```

Docker for Windows 有两种运行模式，一种运行Windows相关容器，一种运行传统的Linux容器。同一时间只能选择一种模式运行。

### WSL安装

```
#安装
wsl --install
#关闭wsl
wsl --shutdown

#检查正在运行的 WSL 版本
wsl -l -v
#将版本从 WSL 1 升级到 WSL 2
wsl --install
#令可用于从 WSL 2 降级到 WSL 1，或将以前安装的 Linux 发行版从 WSL 1 更新到 WSL 2。
wsl --set-version
```

### （一）Docker 常用命令

##### 帮助启动类命令

```shell
• 启动 docker： systemctl start docker
• 停止 docker： systemctl stop docker
• 重启 docker： systemctl restart docker
• 查看 docker 状态： systemctl status docker
• 开机启动： systemctl enable docker
• 查看 docker 概要信息： docker info
• 查看 docker 总体帮助文档： docker --help
• 查看 docker 命令帮助文档： docker 具体命令 --help
docker stats #查看容器状态
```

##### 镜像命令

```shell
#列出本地主机上的镜像
docker images
OPTIONS 说明：
• -a :列出本地所有的镜像（含历史映像层）
• -q :只显示镜像 ID。

#搜索镜像
docker search hello-world
docker search hello-world --limit 5  #限制显示数量
#拉取镜像
docker pull hello-world
docker pull redis:6.0.8 #可以指定版本号
#运行
docker run hello-world

#查看镜像/容器/数据卷所占的空间
docker system df 

#镜像传输
# 将镜像保存成压缩包
docker save -o abc.tar guignginx:v1.0
# 别的机器加载这个镜像
docker load -i abc.tar

#删除某个镜像
docker rmi 某个 XXX 镜像名字 ID
#强制删除
docker rmi -f 镜像 ID 
#删除多个
docker rmi -f 镜像名 1:TAG 镜像名 2:TAG
#删除全部
docker rmi -f $(docker images -qa)
```

##### 容器命令

```shell
#新建+启动容器
docker run [OPTIONS] IMAGE [COMMAND] [ARG...]
#OPTIONS 说明
--name="容器新名字" 为容器指定一个名称；
-d: 后台运行容器并返回容器 ID，也即启动守护式容器(后台运行)；
-i：以交互模式运行容器，通常与 -t 同时使用；
-t：为容器重新分配一个伪输入终端，通常与
-i 同时使用；也即启动交互式容器(前台有伪终端，等待交互)；
-P: 随机端口映射，大写 P
-p: 指定端口映射，小写 p    本机端口:容器端口
--restart=always  #表示docker重启之后，此容器也随之重启，可持续监控

#使用镜像 centos:latest 以交互模式启动一个容器,在容器内执行/bin/bash 命令。
docker run -it ubuntu /bin/bash 


#列出当前所有正在运行的容器
docker ps [OPTIONS]
#OPTIONS 说明
-a :列出当前所有正在运行的容器+历史上运行过的
-l :显示最近创建的容器。
-n：显示最近 n 个创建的容器。
-q :静默模式，只显示容器编号。


#退出容器 两种退出方式
• exit #run 进去容器，exit 退出，容器停止
• ctrl+p+q # run 进去容器，ctrl+p+q 退出，容器不停止

#启动已停止运行的容器
docker start 容器 ID 或者容器名

#重启容器
docker restart 容器 ID 或者容器名

#停止容器
docker stop 容器 ID 或者容器名

#强制停止容器
docker kill 容器 ID 或容器名

#查看停止的容器 
docker ps -a  #列出当前所有正在运行的容器+历史上运行过的
docker ps -l #显示最近创建的容器。

#删除已停止的容器
docker rm 容器 ID

#查看所有容器内ip
docker inspect --format='{{.NetworkSettings.IPAddress}}' $(docker ps -a -q)

#停止所有容器命令：
sudo docker stop $(docker ps -a | awk '{ print $1}' | tail -n +2)

#启动所有容器命令：
sudo docker start $(docker ps -a | awk '{ print $1}' | tail -n +2)

#一次性删除多个容器实例 危险！！
docker rm -f $(docker ps -a -q)
docker ps -a -q | xargs docker rm

#查看容器日志
docker logs 容器 ID

#查看容器内运行的进程
docker top 容器 ID

#查看容器内部细节
docker inspect 容器 ID

#进入正在运行的容器并以命令行交互 不会停止进程!!!
docker exec -it 容器ID bash
#重新进入 会停止进程 尽量使用exec
docker attach 容器ID

#从容器内拷贝文件到主机上
docker cp 容器 ID:容器内路径 目的主机路径
#从主机上拷贝文件到容器内
docker cp 本地文件路径 ID全称:容器路径

#导入和导出容器
export 导出容器的内容留作为一个 tar 归档文件[对应 import 命令]
import 从 tar 包中的内容创建一个新的文件系统再导入为镜像[对应 export]
#案例
docker export 容器ID > 文件名.tar
cat 文件名.tar | docker import - 镜像用户/镜像名:镜像版本号

#忘了添加参数 --restart=always ,可以在后面增加
docker container update --restart=always 容器名字
```

##### 例子

```shell
#使用镜像 centos:latest 以交互模式启动一个容器,在容器内执行/bin/bash 命令。
docker run -it ubuntu /bin/bash 

#使用镜像 centos:latest 以后台模式启动一个容器 
# 然后 docker ps -a 进行查看, 会发现容器已经退出
docker run -d centos  #这个会自动退出

#后台守护式启动 redis
docker run -d redis
docker run -d -p 6379:6379 --name docker-redis redis

#启动ubuntu，交互模式+后台守护 并端口映射 本地8001：容器内部80 随docker启动而启动  并进入命令行
docker run --name ubuntu_lingsheng -itd -p 8002:80 --restart=always   ubuntu  /bin/bash 

#进入正在运行的容器并以命令行交互 不会停止进程!!!
docker exec -it a054da0aef6f  bash

#端口进行映射，将本地 8080 端口映射到容器内部的 80 端口。
docker run --name nginx-test -p 8080:80 -d nginx
```

### （二）Docker 制作镜像commit 操作案例

```shell
#docker commit 提交容器副本使之成为一个新的镜像
docker commit -m="提交的描述信息" -a="作者" 容器 ID 要创建的目标镜像名:[标签名]
```

```shell
#运行ubuntu
docker run -it  ubuntu

#docker 容器内执行上述两条命令： 安装vim
apt-get update
apt-get -y install vim

#创建镜像 类似于继承扩展
#-m 信息 -a 作者 id 64ab1e160dda
docker commit -m "add vim cmd"  -a "xd" 64ab1e160dda myubuntu:1.0
docker commit -m "add ifconfig cmd"  -a "xd" 16a5aea2318b myubuntu:1.1
```



### （三）推送镜像到阿里云

##### 1. 登录阿里云Docker Registry

```
$ docker login --username=纪念忧伤1 registry.cn-hangzhou.aliyuncs.com
```

用于登录的用户名为阿里云账号全名，密码为开通服务时设置的密码。

您可以在访问凭证页面修改凭证密码。

##### 2. 从Registry中拉取镜像

```
$ docker pull registry.cn-hangzhou.aliyuncs.com/ubuntu111/myubuntu:[镜像版本号]
```

##### 3. 将镜像推送到Registry

```shell
$ docker login --username=纪念忧伤1 registry.cn-hangzhou.aliyuncs.com
$ docker tag [ImageId] registry.cn-hangzhou.aliyuncs.com/ubuntu111/myubuntu:[镜像版本号]
$ docker push registry.cn-hangzhou.aliyuncs.com/ubuntu111/myubuntu:[镜像版本号]
```

```
f98c36a1fefe
docker tag f98c36a1fefe registry.cn-hangzhou.aliyuncs.com/ubuntu111/myubuntu:1.0
docker push registry.cn-hangzhou.aliyuncs.com/ubuntu111/myubuntu:1.0
```

请根据实际镜像信息替换示例中的[ImageId]和[镜像版本号]参数。

##### 4. 选择合适的镜像仓库地址

从ECS推送镜像时，可以选择使用镜像仓库内网地址。推送速度将得到提升并且将不会损耗您的公网流量。

如果您使用的机器位于VPC网络，请使用 registry-vpc.cn-hangzhou.aliyuncs.com 作为Registry的域名登录。

##### 5. 示例

使用"docker tag"命令重命名镜像，并将它通过专有网络地址推送至Registry。

```shell
$ docker imagesREPOSITORY                                                         TAG                 IMAGE ID            CREATED             VIRTUAL SIZEregistry.aliyuncs.com/acs/agent                                    0.7-dfb6816         37bb9c63c8b2        7 days ago          37.89 MB
$ docker tag 37bb9c63c8b2 registry-vpc.cn-hangzhou.aliyuncs.com/acs/agent:0.7-dfb6816
```

使用 "docker push" 命令将该镜像推送至远程。

```shell
$ docker push registry-vpc.cn-hangzhou.aliyuncs.com/acs/agent:0.7-dfb6816
```

### （四）本地镜像库

##### 运行私有库 Registry，相当于本地有个私有 Docker hub

```shell
$ docker run -d -p 5000:5000 -v /zzyyuse/myregistry/:/tmp/registry --privileged=true  registry
#cf29cd04d793
```

##### 更新Ubuntu

```shell
docker run -it ubuntu 
#安装ifconfig
apt-get update
apt-get install net-tools
```

##### 制作镜像

```
docker commit -m "add ifconfig cmd"  -a "xd" 16a5aea2318b myubuntu:1.1
```

##### curl 验证私服库上有什么镜像

```
curl -XGET http://192.168.113.132:5000/v2/_catalog
```

##### 将新镜像 zzyyubuntu:1.2 修改符合私服规范的 Tag

```
docker tag myubuntu:1.1 192.168.113.132:5000/myubuntu:1.1
```

##### 修改配置文件使之支持 http

```
vim /etc/docker/daemon.json

{
"registry-mirrors": ["https://aa25jngu.mirror.aliyuncs.com"],
"insecure-registries": ["192.168.113.132:5000"]
}
```

修改完后如果不生效，建议重启 docker,然后重新运行仓库

##### push 推送到私服库

```
docker push 192.168.113.132:5000/myubuntu:1.1
```

##### pull 到本地并运行

```
docker pull  192.168.113.132:5000/myubuntu:1.1
```

### (五) Docker 容器数据卷

卷就是目录或文件，存在于一个或多个容器中，由 docker 挂载到容器，但不属于联合文件系统，因此能够绕过 Union File System 提供一些用于持续存储或共享数据的特性：卷的设计目的就是数据的持久化，完全独立于容器的生存周期，因此 Docker 不会在容器删除时删除其挂载的数据卷

##### 运行一个带有容器卷存储功能的容器实例 -v

```shell
docker run -it --privileged=true -v /宿主机绝对路径目录:/容器内目录 镜像名

#添加读写规则
docker run -it --privileged=true -v /宿主机绝对路径目录:/容器内目录:rw 镜像名
#容器实例内部被限制，只能读取不能写
docker run -it --privileged=true -v /宿主机绝对路径目录:/容器内目录:ro 镜像名
```

文件夹**两边是双向同步的**，可以多个文件夹同步

容器停止后，重启也会同步

#####  容器 2 继承容器 1 的卷规则 --volumes-from

```
docker run -it --privileged=true --volumes-from 父类 --name u2 ubuntu
```

##### 坑：容器卷记得加入

Docker 挂载主机目录访问如果出现cannot open directory .: Permission denied

解决办法：在挂载目录后多加一个--privileged=true 参数即可

#### (六) docker常规软件安装

docker run之后要docker ps看看有没有启动

##### tomcat

```
#tomcat
docker pull tomcat
docker run -it -p 8080:8080 tomcat
docker run -d -p 8080:8080 tomcat

#之后进入tomcat 将webapps.dist 改成webapps
docker exec -it 7a7673026676  bash
rm webapps -r
mv webapps.dist/ webapps

#免修改版说明
docker pull billygoo/tomcat8-jdk8
docker run -d -p 8080:8080 --name mytomcat8 billygoo/tomcat8-jdk8
```

##### mysql

```shell
docker pull mysql
#简单版 没有用
docker run -p 3306:3306 -e MYSQL_ROOT_PASSWORD=123456 -d mysql
docker exec -it 容器 ID /bin/bash
mysql -uroot -p

#实战版启动
docker run -d -p 3306:3306 --privileged=true \
-v /zzyyuse/mysql/log:/var/log/mysql \
-v /zzyyuse/mysql/data:/var/lib/mysql \
-v /zzyyuse/mysql/conf:/etc/mysql/conf.d \
-e MYSQL_ROOT_PASSWORD=123456 \
--name mysql5.7 mysql:5.7
```

###### 默认字符集为latin1 解决中文乱码

> SHOW VARIABLES LIKE 'character%'

```shell
#新建 my.cnf
[client]
default_character_set=utf8
[mysqld]
collation_server = utf8_general_ci
character_set_server = utf8

#通过容器卷同步给 mysql 容器实例
cd /zzyyuse/mysql/conf/
ls
#修改为上面的
vim my.cnf

#重新启动 mysql 容器实例再重新进入并查看字符编码
docker restart mysql
```

docker 安装完 MySQL 并 run 出容器后，建议请先修改完字符集编码后再新建 mysql 库-表-插数据

##### redis

```shell
#入门版 不常用
docker run -d -p 6379:6379 redis
```

###### 实战版启动

#建目录
mkdir -p /app/redis
#将一个 redis.conf 文件模板拷贝进/app/redis 目录下

```
#/app/redis 目录下修改 redis.conf 文件
3.1 开启 redis 验证 可选
requirepass 123

3.2 允许 redis 外地连接 必须
注释掉 # bind 127.0.0.1

3.3daemonize no 必须
将 daemonize yes 注释起来或者 daemonize no 设置，因为该配置和 docker run 中-d 参数冲突，会导致容器一直启动失败

3.4开启 redis 数据持久化 
appendonly yes 可选

3.5关闭保护模式
protected-mode no
```

###### 使用 redis6.0.8 镜像创建容器(也叫运行镜像)

```
docker run -p 6379:6379 --name myr3 --privileged=true -v /app/redis/redis.conf:/etc/redis/redis.conf -v /app/redis/data:/data -d redis redis-server /etc/redis/redis.conf
```

win

```
docker run -p 6379:6379 --name myredis1 --privileged=true -v D:/mydocker/redis/redis.conf:/etc/redis/redis.conf -v D:/mydocker/redis/data:/data -d redis redis-server /etc/redis/redis.conf
```

### 参考

[Docker 教程 | 菜鸟教程 (runoob.com)](https://www.runoob.com/docker/docker-tutorial.html)
