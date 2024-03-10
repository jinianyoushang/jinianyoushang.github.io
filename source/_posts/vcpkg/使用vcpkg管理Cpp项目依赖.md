---
title: 使用vcpkg管理C++项目依赖
typora-root-url: 使用vcpkg管理Cpp项目依赖
tags:
  - vcpkg
  - Clion
  - VS2022
  - Cmake
  -	C++
categories:
  - vcpkg
abbrlink: 67c5155
date: 2024-03-09 16:15:10
cover:
top_img:
---

## 前言

- vcpkg是微软公司开发的一个用于在多个平台上管理C++库的开源工具。它提供了一个简单易用的方式来下载、构建和安装各种C++库，以解决C++开发中的依赖管理问题
- vcpkg支持Windows、macOS和Linux等多个操作系统，为开发人员提供了跨平台的便捷工具来集成和管理C++库。
- 提供了超过2000个C++库，可以一键安装常见的C++库：如Opencv、Qt、openssl、boost、glew、glad等；
- 缺点是，每个库都需要在本地自动编译，有的库安装时间较长。

## vcpkg安装

### 下载

git地址：[microsoft/vcpkg: C++ Library Manager for Windows, Linux, and MacOS (github.com)](https://github.com/Microsoft/vcpkg)

可以使用命令clone下来：

```text
git clone https://github.com/Microsoft/vcpkg.git
```

也可直接在网页上下载zip文件并解压

### 安装

打开clone 下来的vcpkg目录（或解压的目录）

shift+鼠标右键打开powershell（或cmd）

输入如下命令：

```text
.\bootstrap-vcpkg.bat
```

完成后输入

```text
./vcpkg.exe --help
```

查看输出：

![img](v2-cdde573de4635e063b1b54d0fd36d909_720w.webp)

将vcpkg目录加入环境变量中：

环境变量名：VCPKG_ROOT

值： vcpkg的安装目录，我的路径为C:\softwares\vcpkg-master

## 集成到VS2022项目中

1.在vcpkg文件夹中打开powershell或cmd，输入以下命令：

```text
vcpkg integrate project
```

查看输出：

![img](v2-c74d65934469621c3fd58fb33a104216_720w.webp)

2.输入命令：

```text
vcpkg integrate install
```

查看输出：

![img](v2-6e081a5837120b462d7da066ab1dd1c4_720w.png)

3.打开VS2022项目，打开NuGet包管理器：

![img](v2-f3bceec5e0115572e5d2797508ab3194_720w.png)

将vcpkg添加在程序包源中，如图所示：

![img](v2-515c378010a7c08a66ba5a9ccd5eb718_720w.webp)

4.右键工程， 管理NuGet程序包，程序包源选择vcpkg，点击浏览标签页，选择“vcpkg.E.git.vcpkg”并在右侧窗口点击安装

![img](v2-0a7c2c1f359008bc82330af77483bd7e_720w.webp)

安装完成后即可使用vcpkg安装的三方库了。

### 测试使用vcpkg安装boost shared库版，并使用uuid模块进行测试

在vcpkg目录下打开powershell或cmd，输入如下命令安装boost1.81 shared版：

```text
vcpkg install boost:x64-windows
```

vcpkg可支持如下类型：

```text
Available architecture triplets:
vcpkg built-in triplets:
  arm-uwp
  arm64-windows
  x64-linux
  x64-osx
  x64-uwp
  x64-windows-static
  x64-windows
  x86-windows
VCPKG community triplets:
  arm-android
  arm-ios
  arm-linux-release
  arm-linux
  arm-mingw-dynamic
  arm-mingw-static
  arm-neon-android
  arm-uwp-static-md
  arm-windows-static
  arm-windows
  arm64-android
  arm64-ios
  arm64-linux-release
  arm64-linux
  arm64-mingw-dynamic
  arm64-osx-dynamic
  arm64-osx-release
  arm64-osx
  arm64-uwp-static-md
  arm64-uwp
  arm64-windows-static-md
  arm64-windows-static-release
  arm64-windows-static
  arm64ec-windows
  armv6-android
  ppc64le-linux-release
  ppc64le-linux
  riscv32-linux-release
  riscv32-linux
  riscv64-linux-release
  riscv64-linux
  s390x-linux-release
  s390x-linux
  wasm32-emscripten
  x64-android
  x64-freebsd
  x64-ios
  x64-linux-dynamic
  x64-linux-release
  x64-mingw-dynamic
  x64-mingw-static
  x64-openbsd
  x64-osx-dynamic
  x64-osx-release
  x64-uwp-static-md
  x64-windows-release
  x64-windows-static-md
  x64-windows-static-release
  x64-xbox-scarlett-static
  x64-xbox-scarlett
  x64-xbox-xboxone-static
  x64-xbox-xboxone
  x86-android
  x86-freebsd
  x86-ios
  x86-linux
  x86-mingw-dynamic
  x86-mingw-static
  x86-uwp-static-md
  x86-uwp
  x86-windows-static-md
  x86-windows-static
  x86-windows-v120
```

选择自己需要的类型进行安装，可安装多种

等待安装完成：

![img](v2-78d53d13d5a4066fbd1cc8a0365304c6_720w.webp)

打开测试项目，并按照上一步对项目安装NuGet程序包源；

项目添加如下代码：

```cpp
//test.cpp
#define BOOST_UUID_FORCE_AUTO_LINK
#include <iostream>
#include <boost/uuid/uuid.hpp>
#include <boost/uuid/uuid_generators.hpp>
#include <boost/uuid/uuid_io.hpp>
#include <iostream>
using namespace boost::uuids;

int main()
{
	random_generator gen;
	uuid id = gen();
	std::cout << id << '\n';
}
```

![img](v2-7e57caabdfacc8e425090a832cf273fb_720w.webp)

编译并运行：

![img](v2-c8846ffb67a176311057bea3b795088b_720w.webp)

说明boost库在vcpkg中安装成功，并成功在VS2022工程中使用。

## CMake（Clion）+vcpkg

[microsoft/vcpkg: C++ Library Manager for Windows, Linux, and MacOS (github.com)](https://github.com/microsoft/vcpkg#using-vcpkg-with-cmake)

**需要注意的是vcpkg在windows默认用MSVC进行编译，所以在项目中使用也应该用MSVC编译器才可以使用**

### 使用 shell 安装软件包

以eigen3安装为例，在vcpkg的根目录下打开power shell，输入

```text
./vcpkg install eigen3
```

这里在安装成功之后一般会给出包的使用方法提示

```text
find_package(Eigen3 CONFIG REQUIRED)
target_link_libraries(main PRIVATE Eigen3::Eigen)
```

### 简单使用方式

按照官方给出的办法使用包，方便之处在于不需要额外配置头文件和lib的路径，也不需要手动将dll文件放到bin目录下，vcpkg会自动完成这些配置。

如果你没有在环境变量中配置vcpkg的相关变量，而是在CMakeLists中配置vcpkg，需要注意把vcpkg的配置信息放在project之前，如下

```text
cmake_minimum_required(VERSION 3.21.0)

set(VCPKG_ROOT "E:/vcpkg")            # 手动设置到你的vcpkg根目录
set(CMAKE_TOOLCHAIN_FILE "${VCPKG_ROOT}/scripts/buildsystems/vcpkg.cmake")

project(MyProject LANGUAGES CXX)
...
```

### openssl使用例子

安装openssl

```
vcpkg install openssl
```

![image-20240309163359578](image-20240309163359578.png)

复制里面的内容,后面使用

```cmake
find_package(OpenSSL REQUIRED)
target_link_libraries(main PRIVATE OpenSSL::SSL OpenSSL::Crypto)
```

打开Clion，新建项目，在CmakeLists.txt填入上面内容

```cmake
cmake_minimum_required(VERSION 3.26)
set(VCPKG_ROOT "C:/softwares/vcpkg-master")            # 手动设置到你的vcpkg根目录
set(CMAKE_TOOLCHAIN_FILE "${VCPKG_ROOT}/scripts/buildsystems/vcpkg.cmake")
project(clion_demo)

set(CMAKE_CXX_STANDARD 17)
add_compile_options("$<$<C_COMPILER_ID:MSVC>:/utf-8>")
add_compile_options("$<$<CXX_COMPILER_ID:MSVC>:/utf-8>")

add_executable(${PROJECT_NAME} main.cpp)

#这里添加包
find_package(OpenSSL REQUIRED)
target_link_libraries(${PROJECT_NAME} PRIVATE OpenSSL::SSL OpenSSL::Crypto)
```

main.cpp文件如下

```cpp
#include <iostream>
#include <string>
#include <vector>
#include <openssl/rsa.h>
#include <openssl/pem.h>

int test01(){
    // 生成 RSA 密钥对
    RSA *rsa = RSA_generate_key(2048, RSA_F4, nullptr, nullptr);
    if (!rsa) {
        std::cerr << "Failed to generate RSA key pair\n";
        return 1;
    }

    // 加密明文
    std::string plaintext = "Hello, RSA!";
    std::vector<unsigned char> ciphertext(RSA_size(rsa));
    int ciphertext_len = RSA_public_encrypt(plaintext.size(),
                                            reinterpret_cast<const unsigned char *>(plaintext.c_str()),
                                            ciphertext.data(), rsa, RSA_PKCS1_PADDING);
    if (ciphertext_len == -1) {
        std::cerr << "RSA encryption failed\n";
        return 1;
    }

    // 解密密文
    std::vector<unsigned char> decryptedtext(RSA_size(rsa));
    int decryptedtext_len = RSA_private_decrypt(ciphertext_len, ciphertext.data(), decryptedtext.data(), rsa,
                                                RSA_PKCS1_PADDING);
    if (decryptedtext_len == -1) {
        std::cerr << "RSA decryption failed\n";
        return 1;
    }

    // 打印结果
    std::cout << "Plaintext: " << plaintext << std::endl;
    std::cout << "Ciphertext: ";
    for (int i = 0; i < ciphertext_len; ++i) {
        printf("%02x", ciphertext[i]);
    }
    std::cout << std::endl;
    std::cout << "Decryptedtext: "
              << std::string(reinterpret_cast<const char *>(decryptedtext.data()), decryptedtext_len) << std::endl;

    // 释放 RSA 密钥对
    RSA_free(rsa);
    return 0;
}


int test02(){
    // 生成 RSA 密钥对
    RSA* rsa = RSA_new();
    BIGNUM* exponent = BN_new();
    BN_set_word(exponent, RSA_F4);
    if (!RSA_generate_key_ex(rsa, 2048, exponent, nullptr)) {
        std::cerr << "Failed to generate RSA key pair\n";
        return 1;
    }

    // 保存公钥
    std::string pubkey_filename = "publickey.pem";
    FILE* pubkey_file = fopen(pubkey_filename.c_str(), "wb");
    if (!pubkey_file) {
        std::cerr << "Failed to create public key file\n";
        return 1;
    }
    if (!PEM_write_RSAPublicKey(pubkey_file, rsa)) {
        std::cerr << "Failed to write public key to file\n";
        return 1;
    }
    fclose(pubkey_file);

    // 保存私钥
    std::string privkey_filename = "privatekey.pem";
    FILE* privkey_file = fopen(privkey_filename.c_str(), "wb");
    if (!privkey_file) {
        std::cerr << "Failed to create private key file\n";
        return 1;
    }
    if (!PEM_write_RSAPrivateKey(privkey_file, rsa, nullptr, nullptr, 0, nullptr, nullptr)) {
        std::cerr << "Failed to write private key to file\n";
        return 1;
    }
    fclose(privkey_file);

    // 释放资源
    RSA_free(rsa);
    BN_free(exponent);
    return 0;
}

int main() {
    test01();     //加密字符串例子
    test02();      //c++ openssl生成公钥私钥保存到文件 有问题

    return 0;
}
```

运行结果，表示可以成功使用

![image-20240309163710420](image-20240309163710420.png)

### 使用 vcpkg.json 为你的项目配置第三方依赖

可能你的项目需要在多台电脑上编译，如果在每台电脑上都使用shell手动安装依赖库显然不是优雅的解决办法。又或者你的项目需要固定第三方依赖的版本，而vcpkg的命令行似乎没有给出选择软件包版本的选项，难道只能通过不更新vcpkg来使软件库永远保持在某个版本吗？那如果想对某个依赖的版本降级怎么办？

对于自动安装依赖的问题，vcpkg给出的解决办法是，在你的项目的根目录也就是顶级CMakeLists.txt所在目录创建名为vcpkg.json的配置文件：

```text
{
    "name":"MyProject_dependencies",
    "version-semver":"1.0.0",
    "dependencies":[
        "cgal",
        "eigen3",
        "freeimage",
        "glm",
        "glew",
        "flann",
        "lz4"
    ]
}
```

并在环境变量或CMakeLists中配置变量

```text
set(VCPKG_FEATURE_FLAGS "version")          # 用于支持自定义依赖版本
```

如此，不需要在shell手动安装软件包，运行cmake的时候vcpkg会自动将配置文件中的所有依赖安装到你项目的build\vcpkg_installed目录下。

### 关于vcpkg支持哪些库

使用shell命令

```text
./vcpkg search ***
```

## 参考

[VS2022 + vcpkg 使用 - 知乎 (zhihu.com)](https://zhuanlan.zhihu.com/p/615585442)

[使用VCPKG管理你的第三方依赖 - 知乎 (zhihu.com)](https://zhuanlan.zhihu.com/p/477497540)

[vcpkg install | Microsoft Learn](https://learn.microsoft.com/zh-cn/vcpkg/commands/install)

[vcpkg 概述 | Microsoft Learn](https://learn.microsoft.com/zh-cn/vcpkg/get_started/overview)

[microsoft/vcpkg: C++ Library Manager for Windows, Linux, and MacOS (github.com)](https://github.com/microsoft/vcpkg#using-vcpkg-with-cmake)
