---
title: CMake (Clion) 中使用vcpkg
typora-root-url: CMake (Clion) 中使用vcpkg
tags:
  - vcpkg
  - Clion
  - Cmake
categories:
  - vcpkg
abbrlink: d5ac61a7
date: 2024-03-09 16:50:10
cover:
top_img:
---

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
