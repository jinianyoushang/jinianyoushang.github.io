---
title: linux添加动态库搜索路径
typora-root-url: linux添加动态库搜索路径
tags:
  - linux
  - 动态库路径
categories:
  - linux
abbrlink: cf8e0b0
date: 2024-03-08 11:18:03
cover:
top_img:
---



## 方法1：**使用 export 命令：** 在终端中执行以下命令，将内容添加到当前会话的环境变量中：

```bash
vim ~/.bashrc
export LD_LIBRARY_PATH=/path/to/lib:$LD_LIBRARY_PATH 
source ~/.bashrc
```

`LD_LIBRARY_PATH`代表共享库搜索路径

请注意，此方法仅在当前会话中有效，关闭终端后会失效。

## 方法2：**修改用户的 .bashrc文件**

只对当前用户生效

```bash
vim ~/.bashrc
在文件的末尾添加类似下面的行：
export LD_LIBRARY_PATH=/usr/local/lib64:$LD_LIBRARY_PATH
保存文件并退出编辑器。
重新启动终端会话或执行以下命令使更改生效：
source ~/.bashrc
这样，添加的环境变量将在每次登录时自动应用。
```

## 方法3：修改全局的/etc/profile文件

对所有用户生效

```bash
sudo vim /etc/profile 
export  LD_LIBRARY_PATH=/usr/local/lib64:$LD_LIBRARY_PATH
source /etc/profile
```

## 方法4：修改/etc/ld.so.conf文件

```bash
sudo vim /etc/ld.so.conf
#添加要搜索的目录
#执行下面语句可以生效
sudo ldconfig
```
