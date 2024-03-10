---
title: docker容器启动后修改或添加端口
typora-root-url: docker容器启动后修改或添加端口
tags:
  - linux
  - docker
  - 追加端口
categories:
  - docker
abbrlink: 5fdb66a5
date: 2024-03-10 16:24:51
cover:
top_img:
---

docker[容器](https://cloud.tencent.com/product/tke?from_column=20065&from=20065)启动后怎么修改端口映射？在docker run创建并运行容器的时候，可以通过-p指定端口映射规则。但是，也会遇到刚开始忘记设置端口映射或者设置错了需要修改的情况。当docker start运行容器后，并没有提供一个-p选项或设置，让你修改指定端口映射规则。

通常间接的办法是，保存镜像，再创建一个新的容器，在创建时指定新的端口映射。

## 方法一：删除原有容器，重新建新容器

这个解决方案最为简单，把原来的容器删掉，重新建一个。当然这次不要忘记加上端口映射。优点是简单快捷，在测试环境使用较多。缺点是如果是[数据库](https://cloud.tencent.com/solution/database?from_column=20065&from=20065)镜像，那重新建一个又要重新配置一次，就比较麻烦了。

## 方法二：利用docker commit新构镜像

docker commit：把一个容器的文件改动和配置信息commit到一个新的镜像。这个在测试的时候会非常有用，把容器所有的文件改动和配置信息导入成一个新的docker镜像，然后用这个新的镜像重起一个容器，这对之前的容器不会有任何影响。

```bash
docker stop A
docker commit A imageA #将容器commit提交成为一个镜像
docker rm A #删除原镜像
docker run -d -p 80:80 --name A imageA #启动新镜像
```

这种方式的优点是不会影响统一[宿主机](https://cloud.tencent.com/product/cdh?from_column=20065&from=20065)上的其他容器。

## 方法三：修改文件端口，重启docker服务

### 1.停止docker(`一定`要先停止dokcer，不然直接修改配置文件`不会生效`)

```sh
systemctl stop docker
```

### 2.修改这个容器的hostconfig.json文件中的端口（如果config.v2.json里面也记录了端口，也要修改）

```sh
vim /var/lib/docker/containers/[hash_of_the_container]/hostconfig.json
```

在 hostconfig.json 里有 "PortBindings":{} 这个配置项，可以改成 "PortBindings":{"80/tcp":[{"HostIp":"0.0.0.0","HostPort":"8080"}]}

例 hostconfig.json 已删除不相关配置, 只保留格式

```json
{
    "PortBindings": {
        "5700/tcp": [{
            "HostIp": "",
            "HostPort": "10086"
        }],
        "6700/tcp": [{
            "HostIp": "",
            "HostPort": "6700"
        }],
        "9000/tcp": [{
            "HostIp": "",
            "HostPort": "8080"
        }]
    }
}
```

### 3.修改config.v2.json

```sh
vim /var/lib/docker/containers/[hash_of_the_container]/config.v2.json
```

如果没有ExposedPorts 在 config.v2.json 里面添加一个配置项 "ExposedPorts":{"80/tcp":{}} , 将这个配置项添加到 "Tty": true, 前面，如果有就添加 "80/tcp":{} 端口即可。

例 config.v2.json 已删除不相关配置, 只保留格式

注：这里写的都是容器内的端口

```json

{
    "Config": {
        "ExposedPorts": {
            "5700/tcp": {},
            "6700/tcp": {},
            "9000/tcp": {}
        }
    }
} 
```

### 4.重启 docker服务

```sh
systemctl restart docker
```

### 5.查看配置项已经修改成功

```sh
docker inspect  CONTAINER ID
```

![这里是引用](h3t6z66nrt.png)
