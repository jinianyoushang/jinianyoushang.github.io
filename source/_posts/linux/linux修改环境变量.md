---
title: linux修改环境变量
typora-root-url: linux修改环境变量
tags:
 - linux
 - 环境变量
categories:
  - linux
abbrlink: e0ce2883
date: 2024-03-07 21:38:52
cover:
top_img:
---

要将内容添加到 Linux 环境变量中，可以通过以下几种方法进行操作：

1. **使用 export 命令：** 在终端中执行以下命令，将内容添加到当前会话的环境变量中：

   ````bash
   export VARIABLE_NAME="value"
   
   将 `VARIABLE_NAME` 替换为要添加的环境变量的名称，将 `"value"` 替换为要设置的值。
   例如，要将 `/path/to/directory` 添加到 `PATH` 环境变量中，可以执行以下命令：
   
   export PATH="/path/to/directory:$PATH"
   
   这将把 `/path/to/directory` 添加到 `PATH` 变量的开头，保留原有的 `PATH` 值。
   请注意，此方法仅在当前会话中有效，关闭终端后会失效。
   ````

2. **修改用户的 .bashrc 或 .bash_profile 文件：** 如果要将内容添加到特定用户的环境变量中，并在每次登录时自动应用，可以编辑用户的 `.bashrc` 或 `.bash_profile` 文件。这些文件位于用户的主目录下。

   打开终端，并使用文本编辑器（如 `vi` 或 `nano`）打开 `.bashrc` 或 `.bash_profile` 文件：

   ````bash
   vi ~/.bashrc
   在文件的末尾添加类似下面的行：
   export PATH=/home/book/cmake-3.26.0-rc6-linux-x86_64/bin:/home/cs18/vcpkg:$PATH
   保存文件并退出编辑器。
   重新启动终端会话或执行以下命令使更改生效：
   source ~/.bashrc
   这样，添加的环境变量将在每次登录时自动应用。
   ````

3. **修改全局的 /etc/environment 文件：** 如果要在整个系统范围内添加环境变量，可以编辑 `/etc/environment` 文件（需要管理员权限）。

   打开终端，并使用管理员权限打开 `/etc/environment` 文件：

   ````bash
   sudo vi /etc/environment
   在文件中添加类似下面的行：
   PATH="/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/games:/usr/local/games:/snap/bin:/home/cs18/vcpkg"
   保存文件并退出编辑器。
   重新启动系统或注销并重新登录用户，以使更改生效。
   ````

请根据您的需求选择适当的方法，并根据需要设置相应的环境变量。请注意，添加环境变量后，它们将在适当的上下文中可用，并对相关的应用程序生效。
