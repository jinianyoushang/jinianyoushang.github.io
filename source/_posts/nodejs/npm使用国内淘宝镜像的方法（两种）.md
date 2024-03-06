---
title: npm使用国内淘宝镜像的方法（两种）
date: 2024-03-06 11:47:20
tags:
- 镜像
categories:
- node
---

### 一、通过命令配置

#### 1、设置淘宝镜像源

```
npm config set registry https://registry.npmmirror.com/
```

#### 2、设置官方镜像源

```
npm config set registry https://registry.npmjs.org
```

#### 3、查看镜像使用状态：

```
npm config get registry
```

如果返回https://registry.npmmirror.com/ ，说明配置的是淘宝镜像。

如果返回https://registry.npmjs.org/ ，说明配置的是官方镜像。

### 二、通过使用cnpm安装

### 1、安装cnpm

```
 npm install -g cnpm --registry=https://registry.npmmirror.com
 解决安装卡顿或无法安装：

注册模块镜像
 npm set registry https://registry.npmmirror.com
  // node-gyp 编译依赖的 node 源码镜像  
 npm set disturl https://npmmirror.com/dist 
 // 清空缓存  
 npm cache clean --force  
 // 安装cnpm  
 npm install -g cnpm --registry=https://registry.npmmirror.com
```

### 2、使用cnpm

```
cnpm install xxx
```



### 参考

[npm使用国内淘宝镜像的方法（两种）-CSDN博客](https://blog.csdn.net/DongShanYuXiao/article/details/129902599)
