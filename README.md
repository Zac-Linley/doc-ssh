# SSH教程

用于学习的SSH文档，包含了简版的基本原理以及一些使用教程。

欢迎查看[在线文档](https://ssh.lzxblog.top)

# 目录

0 概述

0.1 SSH是一种协议

0.2 OpenSSH开放源代码版本

0.3 常见的SSH工具

0.3.1 适用于Windows的SSH工具

0.3.2 适用于Mac的SSH客户端

1 SSH协议工作原理

1.1 SSH协议框架中的三个子协议

1.2 SSH工作流程

1.3 通过Wireshark抓包分析各个阶段

1.3.1 TCP三次握手

1.3.2 版本协商

1.3.3 算法协商

1.3.4 密钥交换

1.3.5 用户认证

1.3.6 服务请求

2 SSH协议安全性机制

2.1 加密通道

2.2 密钥交换算法

2.2.1 密钥交换算法的基本原理

3 用户认证机制

3.1 基于密码的安全验证

3.2 公钥身份认证

4 SSH密钥

4.1 主机密钥 Host Key

4.2 授权密钥 Authorized Key

4.2.1 用于服务器端的authorized_keys文件

4.3 标识密钥Identity Key

4.4 会话密钥 Session Key

4.5 关于密钥的基础知识

4.5.1 公钥Public Key

4.5.2 私钥Private Key

5 SSH配置文件

5.1 OpenSSH客户端配置文件

5.1.1 配置文件的结构

5.1.2 配置文件详解

5.2 Open SSH服务器端配置文件

5.2.1 配置文件的关系

5.2.2 配置详解

5.3 known_hosts

6 实用教程

6.1 SSH客户端与服务器端安装

6.1.1 Linux验证安装

6.1.2 Linux手动安装SSH

6.1.3 Windows手动安装SSH

6.2 SSH端口号

6.2.1 如何更改SSH Server的默认端口

6.2.2 连接SSH时使用指定端口号

6.3 密钥生成工具ssh-keygen

6.3.1 选择算法和密钥大小

6.3.2 使用ssh-keygen创建主机密钥

6.4 将密钥复制到服务器的工具ssh-copy-id

6.5 实现SSH免密码登录（公钥身份认证）

6.5.1 准备条件

6.5.2 简单配置服务器端

6.6 SSH身份认证代理ssh agent

6.6.1 ssh agent的使用场景

6.6.2 如何使用ssh agent

6.7 其他设置

6.7.1 简化登录

6.8 安全文件传输工具

6.8.1 安全文件传输SFTP

6.8.2 安全文件复制SCP

6.9 SSH命令总结

7 常见问题

7.1 SSH中的中间人攻击

7.2 X11转发 X11-Forwarding

7.2.1 什么是X11-forwarding？

7.2.2 X协议原理简介

7.2.3 支持X11-forwarding的SSH客户端

7.3 SCP和SFTP相同点和区别

7.4 Windows配置自动登录的问题

参考