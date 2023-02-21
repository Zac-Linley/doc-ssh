# 6 实用教程

### 6.1 SSH客户端与服务器端安装 :id=installssh

#### 6.1.1 Linux验证安装

在大多数Linux系统中是默认安装好了的，你可以实用下面的命令检查。

* 成功显示版本号，证明已安装OpenSSH
* 验证服务时原点是绿色的，证明SSH服务正在运行

```shell
#验证ssh版本
user@host:/$ ssh -V
OpenSSH_8.2p1 Ubuntu-4ubuntu0.4, OpenSSL 1.1.1f  31 Mar 2020
#验证SSH服务状态
user@host:/$ systemctl status sshd.service
● ssh.service - OpenBSD Secure Shell server
     Loaded: loaded (/lib/systemd/system/ssh.service; ena>
     Active: active (running) since Sun 2022-02-27 19:28:>
       Docs: man:sshd(8)
             man:sshd_config(5)
    Process: 796 ExecStartPre=/usr/sbin/sshd -t (code=exi>
   Main PID: 824 (sshd)
      Tasks: 1 (limit: 4491)
     Memory: 4.0M
     CGroup: /system.slice/ssh.service
             └─824 sshd: /usr/sbin/sshd -D [listener] 0 o>

مار 03 00:44:39 zac-ubuntu sshd[21852]: pam_unix(sshd:ses>
مار 03 00:44:39 zac-ubuntu sshd[21852]: pam_unix(sshd:ses>
مار 03 00:44:54 zac-ubuntu sshd[21917]: Accepted password>
مار 03 00:44:54 zac-ubuntu sshd[21917]: pam_unix(sshd:ses>
مار 03 00:45:02 zac-ubuntu sshd[21985]: Accepted password>
مار 03 00:45:02 zac-ubuntu sshd[21985]: pam_unix(sshd:ses>
مار 03 19:56:27 zac-ubuntu sshd[26728]: Accepted password>
مار 03 19:56:27 zac-ubuntu sshd[26728]: pam_unix(sshd:ses>
مار 03 21:28:46 zac-ubuntu sshd[28737]: Accepted password>
مار 03 21:28:46 zac-ubuntu sshd[28737]: pam_unix(sshd:ses>
lines 1-22/22 (END)
```

#### 6.1.2 Linux手动安装SSH

安装命令：

```shell
# arch
pacman -S openssh
# ubuntu
apt-get install openssh
# RHEL
yum install openssh
```

启动服务：

```shell
# 启动SSH守护进程
systemctl start sshd
# 开机自启
systemctl enable sshd
```

#### 6.1.3 Windows手动安装SSH

在Window 10（版本1809及更高版本）开始支持OpenSSH，若要安装OpenSSH组件：

1. 打开“设置”，选择“应用”>“应用和功能”，然后选择“可选功能” 。
2. 扫描列表，查看是否已安装 OpenSSH。 如果未安装，请在页面顶部选择“添加功能”，然后：

* 查找“OpenSSH 客户端”，再单击“安装”
* 查找“OpenSSH 服务器”，再单击“安装” 设置完成后，回到“应用”>“应用和功能”和“可选功能”，你应会看到已列出OpenSSH 。

分步教程参考[《Windows-安装SSH服务器》](https://techexpert.tips/zh-hans/windows-zh-hans/%E7%AA%97%E5%8F%A3-%E5%AE%89%E8%A3%85-ssh-%E6%9C%8D%E5%8A%A1%E5%99%A8/)

![guide2022-03-10-23-57-36](https://linley.oss-cn-shanghai.aliyuncs.com/typora\_image/guide2022-03-10-23-57-36.png)

对于Window 10（版本1809以下）以及Windows 7手动安装OpenSSH

1. 下载对用系统版本的安装文件；[GitHub下载连接](https://github.com/PowerShell/Win32-OpenSSH/releases)
2. 将下载好的安装`.zip`文件解压；
3. 将解药好的文件夹整体复制到`C:/Program Files/`路径下，当然你可以放到你电脑上的任何你喜欢的路径下；
4. 在**计算机**(windows7)或**此电脑**(windows10)，右键-->属性-->高级系统设置-->环境变量--系统变量，在此框里面找到`Path`进行编辑;

![guide2022-03-10-23-57-58](https://linley.oss-cn-shanghai.aliyuncs.com/typora\_image/guide2022-03-10-23-57-58.png)

5. windows7系统编辑时候是以文本形式，所以就需要在最后先添加“;”英文分号，再把你安装路径`C:\Program Files\OpenSSH-Win64`粘贴进去；windows 10系统添加比较方便，进到Path里面，点击新建直接粘贴进去；

![guide2022-03-10-23-58-11](https://linley.oss-cn-shanghai.aliyuncs.com/typora\_image/guide2022-03-10-23-58-11.png)

详细教程请参考[《Windows安装OpenSSH支持SSH》](https://www.jianshu.com/p/f8ba3e51d60e)

在Windows上验证安装，打开`cmd`或者`powershell`

```powershell
user@host ssh -V
OpenSSH_for_Windows_8.1p1, LibreSSL 3.0.2
```

设置SSH服务开机自启动

![guide2022-03-10-23-58-34](https://linley.oss-cn-shanghai.aliyuncs.com/typora\_image/guide2022-03-10-23-58-34.png)

### 6.2 SSH端口号 :id=sshport

> 默认状态下，ssh是工作在tcp 22端口。

在SSH的C/S模式中，SSH Server会监听22端口，SSH Client会使用随机端口号（1024\~65535）与SSH Server建立连接。

#### 6.2.1 如何更改SSH Server的默认端口

找到SSH的配置文件：

* 在Linux中：`/etc/ssh/sshd_config`
* 在Windows中：`C:\ProgramData\ssh\sshd_config`

```config
#Port 22
```

取消前面的注释`#`，将`22`修改为你想SSH工作的端口（请不要与其他端口冲突）。

#### 6.2.2 连接SSH时使用指定端口号

```powershell
ssh -p <port-number> user@ip-address
sftp -P <port-number> user@ip-address  #注意P使用大写字母
```

> SFTP使用SSH协议传输数据，所以共用端口号

### 6.3 密钥生成工具`ssh-keygen`

`ssh-keygen`是用于创建新的密钥对的工具。即上面章节中提及的授权密钥与标识密钥组成的密钥对。

以下是操作示例：

```powershell
host (11:39) ~>ssh-keygen 
Generating public/private rsa key pair. 
Enter file in which to save the key (/home/user/.ssh/id_rsa):   #你可以在这一步中指定密钥存储的路径
Enter passphrase (empty for no passphrase):   #你可以在这一步指定一个密码（passphrase）用于保护密钥
Enter same passphrase again:   #再次确认上一步的密码（passphrase）
Your identification has been saved in /home/user/.ssh/id_rsa.   #提示你的私钥保存路径
Your public key has been saved in /home/user/.ssh/id_rsa.pub.   #提示你的公钥保存路径
The key fingerprint is: SHA256:Up6KjbnEV4Hgfo75YM393QdQsK3Z0aTNBz0DoirrW+c user@host 
The key's random art image is: 
+---[RSA 2048]----+ 
|    .      ..oo..| 
|   . . .  . .o.X.| 
|    . . o.  ..+ B| 
|   .   o.o  .+ ..| 
|    ..o.S   o..  | 
|   . %o=      .  | 
|    @.B...     . | 
|   o.=. o. . .  .| 
|    .oo  E. . .. | 
+----[SHA256]-----+ 
host (11:40) ~>
```

!> 上面所有的步骤都可以留空，不用填写任何内容

#### 6.3.1 选择算法和密钥大小

在上面的“The key's random art image is:”下的图中我们可以看到`RSA 2048`，`RSA`就是密钥算法；`2048`就是密钥大小。

```powershell
ssh-keygen -t <arithmetic> -b <size>   #-t后面指定算法名；-b后面指定密钥大小
```

SSH支持多种密钥算法：

* `rsa` 是最早的公钥密码系统之一，被广泛用于安全数据传输。它的安全性取决于整数分解，因此永远不需要安全的RNG（随机数生成器）。与DSA相比，RSA的签名验证速度更快，但生成速度较慢。建议密钥大小至少为2048位。4096位更好。
* `dsa` 一种旧的美国政府数字签名算法。它基于计算离散对数的难度，与RSA相比，DSA的签名生成速度更快，但验证速度较慢。通常密钥大小1024。
* `ecdsa` 是DSA（数字签名算法）的椭圆曲线实现。椭圆曲线密码术能够以较小的密钥提供与RSA相对相同的安全级别。它还具有DSA对不良RNG敏感的缺点。支持三种密钥大小：256、384和521位，建议521位。
* `ed25519` 是EdDSA签名方案，但使用SHA-512/256和Curve25519；它是一条安全的椭圆形曲线，比DSA，ECDSA和EdDSA提供更好的安全性，并且具有更好的性能。这是OpenSSH中添加的新算法，仅少量的客户端支持。

> \[!NOTE]
>
> 1. 根据数学特性，这四种类型又可以分为两大类，dsa/rsa是一类，ecdsa/ed25519是一类，后者算法更先进。
> 2. dsa因为安全问题，已不再使用了。
> 3. ecdsa因为政治原因和技术原因，也不推荐使用。
> 4. rsa是目前兼容性最好的，应用最广泛的key类型，在用ssh-keygen工具生成key的时候，默认使用的也是这种类型。不过在生成key时，如果指定的key size太小的话，也是有安全问题的，推荐key size是3072或更大。
> 5. ed25519是目前最安全、加解密速度最快的key类型，由于其数学特性，它的key的长度比rsa小很多，优先推荐使用。它目前唯一的问题就是兼容性，即在旧版本的ssh工具集中可能无法使用。不过据我目前测试，还没有发现此类问题。 --摘自[《RSA，DSA，ECDSA，EdDSA和Ed25519的区别》](https://blog.51cto.com/u\_15127634/4361097)

#### 6.3.2 使用`ssh-keygen`创建主机密钥

该工具还用于创建主机密钥。主机密钥只是普通的SSH密钥对。对于上面提到的四种算法，每台主机都可以根据这四种算法各生成一对密钥。在Linux中示例如下：

* /etc/ssh/ssh\_host\_dsa\_key
* /etc/ssh/ssh\_host\_dsa\_key.pub
* /etc/ssh/ssh\_host\_ecdsa\_key
* /etc/ssh/ssh\_host\_ecdsa\_key.pub
* /etc/ssh/ssh\_host\_ed25519\_key
* /etc/ssh/ssh\_host\_ed25519\_key.pub
* /etc/ssh/ssh\_host\_rsa\_key
* /etc/ssh/ssh\_host\_rsa\_key.pub

主机密钥通常在安装SSH服务时自动生成。它们也可以随时再生，如果主机密钥发生更改，客户端可能会发出有关已更改密钥的警告。

### 6.4 将密钥复制到服务器的工具`ssh-copy-id`

除了手动将客户端`id-rsa.pub`中的授权密钥复制到服务器端的authorized\_keys内以外，你还可以使用`ssh-copy-id`工具在命令行中完成此操作。

首先确认你已经在客户端生成了一对密钥（公钥-密钥），下面是复制命令：

```powershell
ssh-copy-id  -i <pub_key path> user@serverhost   #<pub_key path>：公钥的文件路径
```

执行成功后客户端的授权密钥就出现在了服务器端的authorized\_keys内

!> 在Windows上是没有`ssh-copy-id`命令的，你可以参考[《windows无法使用ssh-copy-id解决办法》](https://blog.csdn.net/qq\_45624685/article/details/122631083)。

### 6.5 实现SSH免密码登录（公钥身份认证）

#### 6.5.1 准备条件

* [安装好SSH客户端与服务器端](6-shi-yong-jiao-cheng.md#6.1-ssh-ke-hu-duan-yu-fu-wu-qi-duan-an-zhuang-idinstallssh)

#### 6.5.2 简单配置服务器端

1. 为你要登录的用户user创建`~/.ssh`目录以及`~/.ssh/authorized_keys`文件。并设置正确的权限（否则SSH会拒绝连接）：

```powershell
# 确认当前用户为harttle
whoami
# 创建.ssh目录
mkdir ~/.ssh
chmod 700 ~/.ssh
# 创建对方公钥文件
touch ~/.ssh/authorized_keys
chmod 600 ~/.ssh/authorized_keys
```

> 以上命令适用于Linux，在Windows上找到`C:\Users\user\.ssh\`目录，通常`authorized_keys`文件会自动生成，如果没有可以手动创建一个文本文件并将名称改为`authorized_keys`（注意：不能有`.txt`或其他后缀）。

2. 将客户端生成的公钥（授权密钥）`~/.ssh/id_rsa.pub`中的内容复制到服务器端的`~/.ssh/authorized_keys`文件中。

```shell
cat ~/.ssh/id_rsa.pub | ssh user 'cat >> .ssh/authorized_keys'
```

> 在Linux上你也可以使用[`ssh-copy-id`工具](6-shi-yong-jiao-cheng.md#6.4-jiang-mi-yao-fu-zhi-dao-fu-wu-qi-de-gong-ju-sshcopyid)来复制密钥；

现在你就可以通过以下命令实现免认证登陆了

```shell
ssh <user>@<ipaddress>
```

{% hint style="info" %}
在以Windows为服务器端时可能无法配置成功，参考7.4 Windows配置自动登录的问题
{% endhint %}

### 6.6 SSH身份认证代理`ssh agent`

`ssh-agent`是一种控制用来保存公钥身份验证所使用的私钥的程序。

本质上`ssh-agent`就是一个密钥管理器，运行`ssh-agent`以后，使用ssh-add将私钥交给`ssh-agent`保管，其他程序需要身份验证的时候可以将验证申请交给`ssh-agent`来完成整个认证过程。

#### 6.6.1 `ssh agent`的使用场景

* 使用不同的密钥连接到不同的主机时，需要手动指定对应的密钥。ssh-agent 可以帮助我们选择对应的密钥进行认证，不用手动指定密钥即可进行连接。

如果我们在使用`ssh-keygen`生成密钥对时没有使用默认名称id\_rsa作为密钥名称，而是使用其他名称，比如is\_rsa\_test作为密钥名称，那么当我们使用该密钥对远程连接服务器`ssh user@host` 依然会提示输入服务器host的密码。

因为ssh基于密钥进行认证时，默认会使用`~/.ssh/id_rsa`进行认证，当你使用非默认名称的私钥进行认证时，需要手动指明对应的私钥，如果不指明对应的私钥，ssh仍然会默认使用`~/.ssh/id_rsa`进行认证，所以无法认证成功。

虽然可以通过`-i`选项指定对应的私钥文件进行认证，`ssh -i ~/.ssh/id_rsa_test user@host`，但是如果有很多密钥对，使用起来就过于繁琐。而`ssh-agent`则可以帮我们管理这些密钥，避免手动指定密钥，`ssh-agent`会帮助我们选择对应的密钥。使用`ssh-add ~/.ssh/id_rsa_test`将密钥加入到`ssh-agent`中。

* 当私钥设置了密码，我们又需要频繁的使用私钥进行认证时，ssh-agent 可以帮助我们免去重复的输入密码的操作。

如果生成密钥对，同时指定私钥的密码，每次使用私钥进行认证连接是，都会要求输入私钥密码，如果你在当前ssh会话中需要反复的连接到服务器，那么反复的输入复杂的私钥密码，的确会比较麻烦，ssh-agent可以帮助我们，在一个ssh会话中，只要输入一次私钥密码，在同一ssh会话中之后再次使用到相同的私钥时，即可不用再次输入对应密码。

#### 6.6.2 如何使用`ssh agent`

1. 开启`ssh-agent`进程

进入ssh-agent环境:

```shell
#在Linux上
ssh-agent bash  
ssh-agent $SHELL
eval $(ssh-agent)
#在Windows
ssh-agent
#在Windows上也可以到服务里面手动启动OpenSSH Authentication Agent服务
```

此时会发现系统多增加了一个ssh-agent进程

`ssh-agent`参数

| 参数                      | 描述                               |
| ----------------------- | -------------------------------- |
| -a \<bind\_address>     | 将代理绑定到Unix域套接字绑定地址               |
| -d                      | 调试模式                             |
| -D                      | 前台模式。                            |
| -k                      | 把ssh-agent进程杀掉（在Windows上无效）      |
| -s                      | 生成Bourne shell 风格的命令输出           |
| -E \<fingerprint\_hash> | 指定显示密钥指纹时使用的哈希算法。md5或sha256（默认值） |

2. 将密钥添加到`ssh-agent`中

```shell
ssh-add <options> <path>  #<path>密钥的路径
```

`ssh-add`参数

| 参数                      | 描述                                                                       |
| ----------------------- | ------------------------------------------------------------------------ |
| -l                      | 列出当前`agent`中所有的密钥指纹                                                      |
| -L                      | 列出当前`agent`中所有的公钥参数                                                      |
| -E \<fingerprint\_hash> | 指定显示密钥指纹时使用的哈希算法。md5或sha256（默认值）                                         |
| -k                      | 添加密钥到`agent`或从`agent`中移除密钥时跳过证书                                          |
| -K                      | 从FIDO身份验证器加载驻留密钥                                                         |
| -c                      | 使用添加的密钥进行身份认证之前需经过确认                                                     |
| -t                      | 添加密钥时设置生存周期（单位：秒，可以在`sshd_config`中添加`TIME FORMATS h`来更改时间单位，支持s m h d w） |
| -T                      | 通过对每个私钥执行签名和验证操作来测试与指定的私钥是否可用                                            |
| -d                      | 从`agent`中删除密钥                                                            |
| -D                      | 从`agent`中删除全部密钥                                                          |
| -x                      | 使用密码锁定`agent`，即暂停`agent`管理密钥                                             |
| -X                      | 使用密码解锁`agent`                                                            |
| -q                      | 操作成功后保持静默                                                                |
| -h                      | 添加密钥时只用于特定主机或特定目标                                                        |
| -H                      | 指定一个hosts文件用于此密钥在身份认证时查找主机密钥。可以多次指定多个hosts文件，用`,`隔开                      |

### 6.7 其他设置

#### 6.7.1 简化登录

已经配置了[免密码登录（公钥身份认证）](6-shi-yong-jiao-cheng.md#6.5-shi-xian-ssh-mian-mi-ma-deng-lu-gong-yao-shen-fen-ren-zheng)，但是如果你的用户名很复杂（例如用户名是邮箱地址等），或者不想及服务器的IP地址，那么可以更改配置文件来简化流程。

找到配置文件的路径：

* Linux`~/.ssh/config`
* Windows`C:\Users\Lin\.ssh\config`

添加下面的内容到`config`文件中

```config
Host <name>  #自定义的名称
  HostName <ipaddress>  #服务器端主机名称或IP地址
  UserName <user>  #登陆时使用的用户名
```

现在你就可以使用简化的命令了

```shell
ssh <name>
```

> 你还可以参考客户端配置文件详解来添加你需要的功能。

### 6.8 安全文件传输工具

#### 6.8.1 安全文件传输SFTP

sftp是一个文件传输程序，类似于ftp，它使用ssh加密执行所有操作。它还可以使用ssh的公钥身份验证和压缩功能。 可以

* 连接命令

```shell
sftp user@host:path
#URI格式为
sftp://user@host:port/path
```

`sftp`命令参数

| 参数                 | 描述                                                |
| ------------------ | ------------------------------------------------- |
| -a                 | 尝试继续中断的传输，而不是覆盖文件的现有部分                            |
| -b                 | 批处理模式                                             |
| -B \<buffer\_size> | 指定传输文件时使用的缓存大小。较大的缓存需要较少的往返行程，但内存消耗较高。默认值为32768字节 |
| -l                 | 限制带宽，以Kbit/s为单位指定                                 |
| -P                 | 指定要连接的远程主机上的端口                                    |
| -p                 | 保留原始的文件的修改时间、访问时间和模式等                             |
| -4                 | 与ssh命令相同                                          |
| -6                 | 与ssh命令相同                                          |
| -A                 | 与ssh命令相同                                          |
| -C                 | 与ssh命令相同                                          |
| -F                 | 与ssh命令相同                                          |
| -i                 | 与ssh命令相同                                          |
| -J                 | 与ssh命令相同                                          |

* 交互式命令

```
sftp> cd <path> #打开路径<path>
sftp> bye  #退出SFTP
sftp> quit  #退出SFTP
sftp> exit  #退出SFTP
sftp> help  #显示帮助文本
sftp> ?  #显示帮助文本
sftp> ls <path>  #显示当前目录或<path>目录中的目录和文件
sftp> ls -1 <path>  #单列显示当前目录或<path>目录
sftp> ls -a <path>  #列出当前目录或<path>目录以.开头的文件
sftp> ls -f <path>  #不使用首字母排序当前目录或<path>目录
sftp> ls -l <path>  #显示当前目录或<path>目录中的文件和目录的权限、所有权等详细信息
sftp> ls -n <path>  #显示当前目录或<path>目录中的文件和目录的权限、所有权等详细信息，其中用户和组信息以数字形式显示
sftp> ls -r <path>  #使用首字母反向排序当前目录或<path>目录
sftp> ls -S <path>  #按文件大小排序当前目录或<path>目录
sftp> ls -t <path>  #按上次修改时间排序当前目录或<path>目录
sftp> lls <path>  #显示当前目录或<path>目录中的目录
sftp> chgrp <group> <path>  #将<path>文件的文件组修改为<group>
sftp> chmod <mode> <path>  #将<path>文件的模式修改为<mode>
sftp> chown <owner> <path>  #将<path>文件的所有者修改为<owner>
sftp> df -h <path>  #显示保存当前目录或<path>目录的文件系统的使用信息
sftp> df -i <path>  #显示保存当前目录或<path>目录的文件系统的inode信息
sftp> get <remote-path> <local path>  #检索<remote-path>文件或目录并将其储存到本地<local path>，如果未指定本地路径名，则会为其指定与远程计算机上相同的名称。
sftp> get -a <remote-path> <local path>  #尝试恢复现有文件的部分传输
sftp> get -p <remote-path> <local path>  #复制文件权限和访问时间
sftp> lcd <path>  #将本地目录更改为<path>或用户主目录
sftp> lpwd  #打印本地工作目录
sftp> pwd  #打印远程工作目录
sftp> lmkdir <path>  #创建一个本地目录<path>
sftp> mkdir <path>  #创建一个远程目录<path>
sftp> rename <path1> <path2>  #将远程文件从<path1>重命名为<path2>
sftp> ln <path1> <path2>  #创建从<path1>到<path2>的链接
sftp> ln -s <path1> <path2>  #创建从<path1>到<path2>的硬链接
sftp> put <local path> <remote path>  #上载本地<local path>并将其存储在远程<remote path>，如果未指定远程路径名，则会为其指定与本地计算机上相同的名称。
sftp> put -a <local path> <remote path>  #尝试恢复现有文件的部分传输
sftp> put -ap <local path> <remote path>  #复制文件权限和访问时间
sftp> reget <remote-path> <local path>  #等效get -a
sftp> reput <local path> <remote path>  #等效put -a
sftp> rm <path>  #删除指定的远程文件<path>
sftp> rmdir <path>  #删除指定的远程目录<path>
sftp> version  #显示当前sftp协议版本
sftp> !  #跳转到本地shell
sftp> ! <command>  #在本地shell执行命令<command>
```

#### 6.8.2 安全文件复制SCP

SCP是用于在计算机之间复制文件的程序，它使用SSH协议。默认情况下它包含在SSH软件包中

SCP基本用法：

1. 从本地复制到远程主机

* 指定远程目录，复制完成后文件名称不变

```powershell
scp local_file remote_username@remote_ip:remote_folder  #执行后只询问密码
scp local_file remote_ip:remote_folder #执行后询问用户名和密码
```

* 指定文件名称，复制完成后改为指定文件名称

```powershell
scp local_file remote_username@remote_ip:remote_file #执行后只询问密码
scp local_file remote_ip:remote_file #执行后询问用户名和密码
```

* 复制目录`-r`

```powershell
scp -r local_folder remote_username@remote_ip:remote_folder #执行后只询问密码
scp -r local_folder remote_ip:remote_folder #执行后询问用户名和密码
```

2. 从远程主机复制到本地

> 只要将从本地复制到远程的命令的后2个参数调换顺序即可

```powershell
scp remote_username@remote_ip:remote_file local_file 
scp -r remote_username@remote_ip:remote_folder local_folder
```

3. 指定端口号`-P`

> 使用大写`P`

```powershell
#scp命令使用端口号4588
scp -P 4588 remote_username@remote_ip:remote_folder
```

4. 免认证

配置[SSH公钥身份验证](6-shi-yong-jiao-cheng.md#6.5-shi-xian-ssh-mian-mi-ma-deng-lu-gong-yao-shen-fen-ren-zheng)后即可实现免认证。

### 6.9 SSH命令总结

> 语法格式`ssh [参数] [远程主机]`

| 参数         | 描述                                          |
| ---------- | ------------------------------------------- |
| -1         | 强制使用ssh协议版本1                                |
| -2         | 强制使用ssh协议版本2                                |
| -4         | 强制使用IPv4地址                                  |
| -6         | 强制使用IPv6地址                                  |
| -A         | 开启认证代理连接转发功能                                |
| -a         | 关闭认证代理连接转发功能                                |
| -b \<IP地址> | 使用本机指定的地址作为对位连接的源IP地址                       |
| -C         | 请求压缩所有数据                                    |
| -F <配置文件>  | 指定ssh指令的配置文件，默认的配置文件为“/etc/ssh/ssh\_config” |
| -f         | 后台执行ssh指令                                   |
| -g         | 允许远程主机连接本机的转发端口                             |
| -i <身份文件>  | 指定身份文件（即私钥文件）                               |
| -l <登录名>   | 指定连接远程服务器的登录用户名                             |
| -N         | 不执行远程指令                                     |
| -o <选项>    | 指定配置选项                                      |
| -p <端口>    | 指定远程服务器上的端口                                 |
| -q         | 静默模式，所有的警告和诊断信息被禁止输出                        |
| -X         | 开启X11转发功能                                   |
| -x         | 关闭X11转发功能                                   |
| -y         | 开启信任X11转发功能                                 |

```powershell
#示例
ssh -p <port-number> -- user -i C:\Users\user\.ssh\custom_config <ip-address>
```
