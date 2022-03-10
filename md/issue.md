## 7.1 SSH中的中间人攻击 :id=mitm

黑客和恶意软件会攻击路由器、DSL解调制器和WiFi路由器，在上面安装恶意软件来执行中间人攻击，安装了恶意软件的设备被称为**攻击工具**。

在SSH协议中，主机密钥只能由root用户访问。如果攻击者获得了服务器的root访问权限，他当然可以获得主机密钥的副本。

一旦攻击者获取到服务器主机密钥的副本，当客户端连接时，就可以在攻击工具上，冒充SSH服务端与客户端建立连接，骗取服务器端的密码或密钥。

然后再攻击工具上充当客户端，与服务器建立另一个加密连接。

## 7.2 X11转发 X11-Forwarding :id=x11frowarding

### 7.2.1 什么是X11-forwarding？

X11中的X指的就是X协议；11指的是采用X协议的第11个版本。
X11-forwarding说的简单明白点就是：可以通过一个支持X Server的SSH客户端，例如：MobaXterm。 连接到远程Linux服务器，可以在本地通过MobaXterm运行操作一个远程Linux服务器上有图形界面的程序。

### 7.2.2 X协议原理简介

<img src="https://linley.oss-cn-shanghai.aliyuncs.com/typora_image/issue2022-03-11-00-00-37.png" alt="issue2022-03-11-00-00-37" width="" height="">

Linux系统本身是没有图形化界面的，所谓的图形化界面系统只不过中Linux下的应用程序，所有的Linux系统的图形化界面应用程序底层都是基于X协议。

X协议由X server和X client组成：

- server管理主机上与显示相关的硬件设置（如显卡、硬盘、鼠标等），它负责屏幕画面的绘制与显示，以及将输入设置（如键盘、鼠标）的动作告知X client。

- X client (即 X 应用程序) 则主要负责事件的处理（即程序的逻辑）。

举个例子，如果用户点击了鼠标左键，因为鼠标归X server管理，于是X server就捕捉到了鼠标点击这个动作，然后它将这个动作告诉X client，因为X client负责程序逻辑，于是X client就根据程序预先设定的逻辑（例如画一个圆），告诉X server说：“请在鼠标点击的位置，画一个圆”。最后，X server就响应X client的请求，在鼠标点击的位置，绘制并显示出一个圆。

### 7.2.3 支持X11-forwarding的SSH客户端

- XShel- 6
- MobaXterm 11

## 7.3 SCP和SFTP相同点和区别

都是使用SSH协议来传输文件的。登录时的用户信息都是经过SSH加密后才传输的，所以说SCP和SFTP实现了安全的文件传输。
SCP和SCP命令相似，SFTP和FTP的使用方法也类似。SCP和SFTP的共同之处在于（**使用SSH将文件加密才传输的**）

SCP和SFTP的不同之处：
- SCP比较简单，是轻量级的，SFTP的功能则比较多。
- SFTP在文件传输过程中中断的话，连接后还可以继续传输，但SCP不行。

## 7.4 Windows配置自动登录的问题 :id=issueauthshhbywindows

以Windows作为服务端并按照[6.5 实现SSH免密码登录（公钥身份认证）](md/guide#sshpubauth)中的步骤配置时，在连接时依然要求输入密码。

我尝试重启了服务端的`OpenSSH SSH Server`服务，依然没有效果。

使用`ssh -vvv`查看调试信息

```log
debug3: send packet: type 50
debug2: we sent a keyboard-interactive packet, wait for reply
debug3: receive packet: type 51 
debug1: Authentications that can continue: publickey,password,keyboard-interactive
```
这是以Ubuntu为服务器端自动登录成功的调试信息

```log
debug3: sign_and_send_pubkey: signing using rsa-sha2-512
debug3: send packet: type 50
debug3: receive packet: type 52
debug1: Authentication succeeded (publickey).
Authenticated to 10.0.0.20 ([10.0.0.20]:2000).
```
可以看出在以Windows为服务器端时，客户端发送了密钥，但是服务器端并没有回应。

可以通过阿里云盘获取完整的调试信息 [下载连接](https://www.aliyundrive.com/s/kQqRbquLCVa)

## SSH隧道 :id=sshtunnel