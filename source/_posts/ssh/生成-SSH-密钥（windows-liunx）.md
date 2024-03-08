---
title: 生成 SSH 密钥（windows+liunx）
typora-root-url: 生成 SSH 密钥（windows+liunx）
tags:
  - ssh
categories:
  - ssh
abbrlink: e253486a
date: 2024-03-07 17:18:09
cover:
top_img:
---

# 生成 SSH 密钥（windows+liunx）

## **要在 Windows 10 上生成 SSH 密钥，您可以按照以下步骤操作：**

1.打开 PowerShell（或者按下 Windows + X 并选择 “Windows PowerShell”）；

2.在 PowerShell 中输入以下命令来生成 SSH 密钥：

```text
ssh-keygen -t rsa
```

3.输入您要保存密钥文件的路径和名称。例如：C:\Users\YourName.ssh\id_rsa。您可以选择保留默认名称，因为它已经被 SSH 所识别（id_rsa）；

4.在弹出的窗口中输入密码，或者直接敲击回车键选择不设置密码；

5.这个时候，您会得到一个公共密钥（id_rsa.pub）和一个私有密钥（id_rsa）。

现在您已经成功地生成了 SSH 密钥对。公钥是要提供给远程服务器的，私钥则要保留在本地，并加以保护。

## **在 Linux 上生成 SSH 密钥的步骤与在 Windows 上类似。您可以按照以下步骤操作：**

1.打开终端（或者按下 Ctrl + Alt + T）；

2.输入以下命令来生成 SSH 密钥：

```text
ssh-keygen -t rsa
```

3.输入您要保存密钥文件的路径和名称，或者直接敲击回车键选择默认值，例如：/home/YourName/.ssh/id_rsa；

4.在弹出的窗口中输入密码，或者直接敲击回车键选择不设置密码；

5.这个时候，您会得到一个公共密钥（id_rsa.pub）和一个私有密钥（id_rsa）。

现在您已经成功地生成了 SSH 密钥对。公钥是要提供给远程服务器的，私钥则要保留在本地，并加以保护。另外，如果您想要让 SSH 代理管理您的密钥，您还需要在本地配置 SSH 代理。
