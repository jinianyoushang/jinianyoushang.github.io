---
title: Visual Studio CMake中文UTF8编码设置
typora-root-url: Visual Studio CMake中文UTF8编码设置
tags:
  - cmake
  - Cpp
  -	UTF8
categories:
  - Cpp
abbrlink: 42ff7d67
date: 2024-03-14 09:57:54
cover:
top_img:
---

### 问题

使用Cmake调用MSVC编译utf-8格式的cpp文件时，如果里面有中文会编译出错。如下图：

![image-20240314094223549](image-20240314094223549.png)

### 解决方法


1. 文件-高级保存选项，选择utf-8编码，无签名格式
2. 在**add_executable之前**加入:

```cmake
#msvc支持中文 utf8
add_compile_options("$<$<C_COMPILER_ID:MSVC>:/utf-8>")
add_compile_options("$<$<CXX_COMPILER_ID:MSVC>:/utf-8>")
```

