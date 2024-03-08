---
title: linux和windows共享文件夹
typora-root-url: linux和windows共享文件夹
tags:
  - linux
  - windows
  - 共享文件夹
categories:
  - linux
abbrlink: a059173d
date: 2024-03-07 21:39:52
cover:
top_img:
---

### ubuntu访问windows

```
sudo mount -t cifs //10.170.27.210/ ~/share -o username='***@qq.com',password='***',vers=2.0

sudo busybox  mount -t cifs //10.170.27.210/topographic_map ~/share -o username=***@qq.com,password=***,vers=2.0
```

### windows访问ubuntu

#### 确认系统中安装samba

```
sudo apt-get install samba
```

#### 新建用户

```
sudo groupadd xdsmb -g  6000
sudo useradd  xdsmb -u 6000 -g 6000 -s /sbin/nologin -d /dev/null
```

#### 设置你的账户samba密码

```
sudo touch /etc/samba/smbpasswd
sudo smbpasswd -a xdsmb    #usr 表示你的用户名
```

然后按照提示输入密码就可以了。

密码 ***

#### 将你的用户添加到sambashare用户组中

```
sudo adduser xdsmb sambashare
```

#### 具体共享文件方法

右击你想共享的文件夹，点击本地网络共享，然后点击共享文件夹就可以了。

#### 怎么在Windows中访问这个共享文件夹

方法跟Windows访问Windows是一样的，按Windows+R，然后输入

#### 命令行配置共享文件夹

[ubuntu命令行配置文件夹共享_重启ubuntu共享问价夹命令-CSDN博客](https://blog.csdn.net/lantian6/article/details/107250008)

### 问题解决

##### [Ubuntu](https://so.csdn.net/so/search?q=Ubuntu&spm=1001.2101.3001.7020)挂载nfs文件系统报错：mount: /mnt/nfs: bad option; for several filesystems (e.g. nfs, cifs) you might need a /sbin/mount.<type> helper program.。

[高版本Ubuntu挂载nfs文件系统报错：mount: /mnt/nfs: bad option； for several filesystems (e.g. nfs, cifs)_bad option; for several filesystems (e.g. nfs, cif-CSDN博客](https://blog.csdn.net/weixin_43782998/article/details/109788521)

#### 参考

[Ubuntu：与Windows共享文件夹_ubuntu访问windows共享文件夹-CSDN博客](https://blog.csdn.net/rangfei/article/details/124225799#:~:text=3.1 Ubuntu 下设置共享目录 1 对要共享的目录右击 -> Local Network,2 选择Share this folder 3 安装共享服务 GUI方式安装 提示安装samba包)