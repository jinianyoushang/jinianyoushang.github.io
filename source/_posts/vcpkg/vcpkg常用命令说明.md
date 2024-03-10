---
title: vcpkg常用命令说明
typora-root-url: vcpkg常用命令说明
tags:
  - vcpkg
  - C++
categories:
  - vcpkg
abbrlink: aa152842
date: 2024-03-09 16:55:10
cover:
top_img:
---
#### 搜索可用库

```
vcpkg search ***
```

#### 安装三方库

````
vcpkg install boost:x64-windows

#移除一个开源库（已安装）注意：1. 默认移除 x86-winodws 版本库，如需移除其他版本库，需指定。
#2. 只移除库本身，源码包和解压后的源码并未移除。
vcpkg remove curl
````

默认情况下，Vcpkg使用的编译环境是x86-windows, x64-linux.cmake或x64-osx.cmake。

#### 列出已安装的开源库

```
vcpkg list
```

#### 更新已安装的开源库

想要更新已安装的开源库，一般有两个指令：一个是update指令，可以显示可以升级的开源库的列表；另一个是upgrade的指令，会重新编译所有需要更新的包。

#### 导出已安装的开源库

想要导出已安装的开源库，那么执行 export 指令即可。比如我们要导出 curl 库：

```
vcpkg export curl --7zip

#注意：默认导出 x86-windows 版本库，如需导出其他版本，需指定。
默认导出 vcpkg 目录下，默认导出包名称 vcpkg-export-日期-时间，如需指定目录和名称，使用 “-output=” 参数。
　　　　　导出必须指定包格式：
　　　　　　　--raw：以目录格式导出
　　　　　　　--nuget：以 nuget 格式导出
　　　　　　　--ifw：未知
　　　　　　　--zip：以 zip 格式导出
　　　　　　　--7zip：以 7z 格式导出
```

#### 导入已备份的开源库

```
 vcpkg import xxx.7z
```

#### 集成至Visual Studio中(Windows)

```
vcpkg integrate install

#取消集成
vcpkg integrate remove
```

#### 好用的库-安装多个库

```
vcpkg install opencv4 eigen3 glm freeimage opencv opencv[contrib] opencv[ade] opencv[cuda] ceres opengl ceres boost glad glew --keep-going
```

#### 解决网速慢

**方法1：先下载，后编译**

```
vcpkg.exe install ceres --only-downloads
vcpkg.exe install ceres
```

**方法2：改端口**

```
如果有proxy（没有就不用看了），下载还是很慢，就是端口号没设置好。
在powershell中，注意是powershell而不是dos（不会真的有人用dos配置vcpkg吧），输入如下代码设置环境变量：
下面只是临时改环境变量，

$env:HTTP_PROXY="localhost:4780"
$env:HTTPS_PROXY="localhost:4780"
```

#### 常用命令

```
集成到全局：vcpkg integrate install
移除全局：vcpkg integrate remove
集成到工程：vcpkg integrate project（在“\scripts\buildsystems”目录下，生成nuget配置文件）
查看库目录：vcpkg search
查看支持的架构：vcpkg help triplet
指定编译某种架构的程序库：vcpkg install xxxx:x64-windows（x86-windows）
卸载已安装库：vcpkg remove xxxx
指定卸载平台：vcpkg remove xxxx:x64-windows
移除所有旧版本库：vcpkg remove --outdated
查看已经安装的库：vcpkg list
更新已经安装的库：vcpkg update xxx
导出已经安装的库：vcpkg export xxxx --7zip（–7zip –raw –nuget –ifw –zip）
```

#### vcpkg 目录文件及文件夹说明

```
buildtrees - 包含从中生成每个库的源的子文件夹，一般在xxxx.clean文件夹下。
docs - 文档和示例。
downloads - 所有已下载的工具或源的缓存副本。 运行安装命令时，vcpkg 会首先搜索此处。
installed - 包含每个已安装库的标头和二进制文件。 与 Visual Studio 集成时，实质上是相当于告知它将此文件夹添加到其搜索路径。
packages - 在不同的安装之间用于暂存的内部文件夹。
ports - 用于描述每个库的目录、版本和下载位置的文件。 如有需要，可添加自己的端口。
scripts - 由 vcpkg 使用的脚本（CMake、PowerShell）。
toolsrc - vcpkg 和相关组件的 C++ 源代码。
triplets - 包含每个受支持目标平台（如 x86-windows 或 x64-uwp）的设置。

更新包指令 vcpkg upgrade --no-dry-run
```

#### 参考

[vcpkg国内镜像使用方法 - 知乎 (zhihu.com)](https://zhuanlan.zhihu.com/p/383683670)

[Vcpkg——C++包管理工具 - 知乎 (zhihu.com)](https://zhuanlan.zhihu.com/p/87391067)

[vcpkg：跨平台 C++ 包管理器的安装教程 - hik_wxy - 博客园 (cnblogs.com)](https://www.cnblogs.com/hik-wxy/p/14744272.html)