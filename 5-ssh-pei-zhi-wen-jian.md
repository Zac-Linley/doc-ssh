# 5 SSH配置文件

### 5.1 OpenSSH客户端配置文件

配置文件的保存位置：

* Linux `~/.ssh/config/etc/ssh/ssh_config`
* Windows `C:\Users\user\.ssh\config`

通常情况下配置文件是不存在的，你可以手动创建它

* Linux `touch ~/.ssh/config&&chmod 600 ~/.ssh/config`
* Windows 到`C:\Users\user\.ssh\`路径下手动创建文本文件并删除`.txt`后缀

#### 5.1.1 配置文件的结构

```config
Host hostname1
    SSH_OPTION value
    SSH_OPTION value

Host hostname2
    SSH_OPTION value

Host *
    SSH_OPTION value
```

* config的结构如上所示，是一块一块的。
* 缩进其实不是必须的，但是有缩进非常有助于查看。
* 每一块使用Host指令开始，每一个Host块可以针对一个或者多个需要匹配的内容（多个使用空格分隔），也可以采用下面这些特殊标识符：
  * `*`匹配0或者任意个字符。比如`Host *`匹配所有的Hosts，`192.168.0.*`匹配`192.168.0.0/24`这个subnet下的IP。
  * `?`匹配一个字符。如`10.10.0.?` 匹配`10.10.0.[0-9]`。
  * `!`取反，即不匹配。比如`Host 10.10.0.* !10.10.0.5`匹配`10.10.0.0/24 subnet`但是`10.10.0.5`除外。

匹配规则： SSH客户端是从上往下匹配的，因此如果有多个Host匹配命中，其中定义的配置参数第一个出现的生效，比如第一个匹配配置组里面有User配置，最后匹配到的配置组里面也有，那是第一个匹配到的User会被实际使用。所以建议将比较具体的匹配放在上方，通用的放在后面(比如针对某一个ip段的放在上面，针对所有的放在最后面)。

#### 5.1.2 配置文件详解 :id=clientconfig

* `Host host-name` 指定配置块，必配项
* `user username` 指定登录用户，必配项
* `hostname 10.0.0.1` 指定服务器地址，通常用ip地址，必配项
* `port 22` 指定认证私钥文件，必配项
* `Identityfile ~/.ssh/id_rsa` 指定本地认证私钥地址
* `IdentityFile ~/.ssh/id_rsa.pub` 指定认证私钥文件
* `ForwardAgent yes` 允许ssh-agent转发
* `ForwardX11 yes` 允许许X11转发#X11转发
* `ForwardX11Trusted yes` 远程X11客户端将具有对原始X11显示器的完全访问权限。
* `DynamicForward 2000` 指定通过安全通道转发的本地端口
* `RemoteForward 3000` 指定远程主机上的一个TCP端口通过安全通道转发到本地主机上的指定主机和端口。第一个参数必须是：`[bind_address:]port`，第二个参数必须是`host:port`。#SSH隧道
* `LocalForward` 指定本地机器上的一个TCP端口通过安全通道转发到远程机器上的指定主机和端口。第一个参数必须是\[bind\_address:]port，第二个参数必须是host:port。
* `ExitOnForwardFailure yes` 当动态、隧道、本地和远程端口转发设置失败时，是否应终止连接。
* `ClearAllForwardings` 指定清除在配置文件中或在命令行上指定的所有`local`、`remote`和`dynamic`端口发。
* `GatewayPorts 2001` 指定是否允许远程主机连接到本地的端口
* `IdentitiesOnly no` 指定ssh是否仅使用配置文件或命令行指定的私钥文件进行认证。值为`yes`或`no`，默认为`no`，该情况可在ssh-agent提供了太多的认证文件时使用。
* `StrictHostKeyChecking ask`
  * `ask`：默认值，第一次连接陌生服务器时提示是否添加，同时如果远程服务器公钥改变时拒绝连接；
  * `yes`：不会自动添加服务器公钥到`~/.ssh/known_hosts`中，同时如果远程服务器公钥改变时拒绝连接；
  * `no`：自动增加新的主机键到`~/.ssh/known_hosts`中。
* `BindAddress 10.0.0.1` 指定使用本地计算机上的指定地址作为连接的源地址。
* `Protoco- 2` 按首选项顺序指定协议版本。多个版本必须以逗号分隔。
* `Cipher <char>` 在SSH v1中设置加密用的密码。
* `Ciphers <char>`
  * 按首选项顺序指定SSH v2允许的密码。
  * 多个密码必须以逗号分隔。
  * 命令`ssh -Q cipher`可用于查询支持的密码。
* `HostKeyAlgorithms ssh-ed25519` 指定客户端要使用的SSH v2的主机密钥算法，按优先级排序。
* `HostKeyAlias username` 指定一个别名，当在密钥数据库文件中查找或保存主机钥匙时，使用该别名而不是真实的主机名称。
* `GlobalKnownHostsFile <path>` #指定要用于全局主机密钥数据库的文件，而不是`/etc/ssh/sh_known_hosts`。
* `HashKnownHosts yes` 将主机名和地址添加到`~/.ssh/known_hosts`时应对其进行哈希处理。
* `Compression no` 指定是否使用压缩。
* `CompressionLeve- 1` 指定在启用压缩时要使用的压缩级别。
* `EscapeChar ~` 设置escape按键。
* `ConnectionAttempts 10` 指定退出前要进行的尝试次数。
* `ConnectTimeout 10` 指定连接到SSH服务器时使用的超时（以秒为单位），而不是使用默认系统TCP超时。
* `ControlMaster 3` 允许通过单个网络连接共享多个会话。
* `ControlPath <path>` #指定用于连接共享的控制socket的路径，或指定字符串`none`以禁用连接共享。
* `RhostsAuthentication no` 设置是否使用基于rhosts的安全验证。
* `RhostsRSAAuthentication no` 设置是否使用用RSA算法的基于rhosts的安全验证。
* `HostbasedAuthentication yes` 指定是否使用用户主目录中的`.rhosts`或`.shosts`文件以及全局配置中的/`etc/hosts.equiv`和`/etc/shosts.equiv`，尝试基于rhosts的认证与公钥认证。
* `RSAAuthentication yes` 设置是否使用RSA算法进行安全验证。
* `PasswordAuthentication yes` 设置是否使用口令验证。
* `FallBackToRsh no` 设置如果用ssh连接出现错误是否自动使用rsh。
* `UseRsh no` 设置是否在这台计算机上使用rlogin/rsh。
* `BatchMode no` 如果设为`yes`，passphrase/password（交互式输入口令）的提示将被禁止。当不能交互式输入口令的时候，这个选项对脚本文件和批处理任务十分有用。
* `CheckHostIP yes` 设置ssh是否查看连接到服务器的主机的IP地址以防止DNS欺骗。建议设置为`yes`。
* `StrictHostKeyChecking no` 如果设置成`yes`，ssh就不会自动把计算机的密匙加入`$HOME/.ssh/known_hosts`文件，并且一旦计算机的密匙发生了变化，就拒绝连接。
* `KbdInteractiveAuthentication yes` 指定是否使用键盘交互式身份验证。这是密码身份验证、一次性密码和多重身份验证的常用方法。
* `KbdInteractiveDevices`
* `LocalCommand ipconfig` 指定成功连接到服务器后要在本地计算机上执行的命令。
* `LogLeve- ERROR` 指定来自ssh的日志信息的粗略程度。可以是QUIET,FATAL,ERROR,INFO,VERBOSE,DEBUG,DEBUG1,DEBUG2,DEBUG3。
* `Macs hmac-sha1` 按优先顺序指定Mac（消息验证码）算法，命令`ssh -Q mac`可以用来查询支持的Mac算法。
* `NoHostAuthenticationForLocalhost yes` 如果主目录是跨机器共享的，就可以使用这个选项。在这种情况下，localhost将指的是每台机器上的不同机器，而用户将得到许多关于改变主机密钥的警告。
* `PreferredAuthentications` 指定客户端应尝试SSH v2身份验证方法的顺序。
* `ProxyCommand` 指定用于连接到服务器的命令。SSH客户端使用其标准输入和标准输出与代理命令进行通信，代理命令应将通信传递给SSH服务器。
* `PubkeyAuthentication yes` 指定是否使用SSH密钥尝试公钥认证。
* `SendEnv` 指定应该向服务器发送哪些环境变量。
* `ServerAliveCountMax 10` 设置客户端发送的`keepalive`的数量，而客户端没有收到来自服务器的任何信息反馈。当达到这个阈值时，客户端将终止会话。
* `ServerAliveInterva- 5` 指定向服务器发送`keepalive`信息的时间间隔。这些信息是通过加密通道发送的，用于检测服务器是否崩溃或网络是否中断。
* `TCPKeepAlive yes` 是否向对方发送TCP keepalives。这是在TCP协议层面上的操作。
* `Tunne- yes` 请求在客户端和服务器之间进行设备转发。这用于通过SSH实现VPN
* `TunnelDevice remote_tun` 要打开tun的设备，local\_tun或remote\_tun
* `UsePrivilegedPort no` 指定是否使用特权端口进行外发连接。
* `UserKnownHostsFile <path>` 指定一个文件，用户的已知主机密钥数据库，而不是默认的`~/.ssh/known_hosts`。
* `VerifyHostKeyDNS yes` 指定是否使用DNS和SSHFP资源记录来验证远程密钥。
* `VisualHostKey yes` 在登录时或对于未知主机钥匙，除了十六进制指纹字符串外，是否还打印远程主机钥匙指纹的ASCII艺术图。

### 5.2 Open SSH服务器端配置文件

OpenSSH服务器在启动时读取配置文件，通常配置文件的保存位置：

* Linux `/etc/ssh/sshd_config`
* Windows `C:\ProgramData\ssh\sshd_config`

#### 5.2.1 配置文件的关系

![config2022-03-10-23-55-30](https://linley.oss-cn-shanghai.aliyuncs.com/typora\_image/config2022-03-10-23-55-30.png)

#### 5.2.2 配置详解

1. 关于 SSH Server 的整体设定，包含使用的 port 啦，以及使用的密码演算方式

* `Port 22` SSH 默认使用 22 这个 port，您也可以使用多的port
* `Protoco- 2,1` 选择的SSH协议版本，可以是1也可以是2，如果要同时支持两者，就必须要使用2,1 这个分隔了
* `ListenAddress 10.0.0.1`　只监听来自10.0.0.1的SSH联机。（假设有两个IP 10.0.0.1与10.0.0.2 ）
* `PidFile /var/run/sshd.pid`　可以放置SSHD这个PID的档案,左列为默认值
* `LoginGraceTime 600`　连接等待时间（单位秒）
* `Compression yes`　是否可以使用压缩指令 　

2. 说明主机的 Private Key 放置的档案，默认使用下面的档案即可

* `HostKey /etc/ssh/ssh_host_key`　SSH v1使用的私钥
* `HostKey /etc/ssh/ssh_host_rsa_key`　SSH v2使用的RSA私钥
* `HostKey /etc/ssh/ssh_host_dsa_key`　SSH v2使用的DSA私钥
* `KeyRegenerationInterva- 3600`　 重新建立主机密钥的时间间隔
* `ServerKeyBits 768` 　Server key的长度

3. 关于登录文件的讯息数据放置与daemon的名称

* `SyslogFacility AUTH`　　当有人使用SSH登入系统的时候，SSH会记录信息在daemon name下，默认是以 AUTH 来设定的，即`/var/log/secure`里面，其它可用的daemon name为：DAEMON,USER,AUTH,LOCAL0,LOCAL1,LOCAL2,LOCAL3,LOCAL4,LOCAL5,
* `LogLeve- INFO`　　登录记录的等级

4. 登入设定部分

* `PermitRootLogin no`　是否允许root登入。默认是允许的，但是建议设定成 no
* `UserLogin no`　在SSH底下本来就不接受login这个程序的登入
* `StrictModes yes`　使用者的host key改变之后，Server就不接受新的SSH连接
* `RSAAuthentication yes`　是否完全使用RSA认证，仅针对SSH v1
* `PubkeyAuthentication yes`　是否允许Public Key 只针对SSH v2
* `AuthorizedKeysFile .ssh/authorized_keys` 密钥身份认证的密钥路径

5. 认证部分

* `RhostsAuthentication no`　是否使用`.rhosts`，因为不安全了，所以通常禁用
* `IgnoreRhosts yes`　是否取消使用`~/.ssh/.rhosts`
* `RhostsRSAAuthentication no` 是否使用`/etc/hosts.equiv`中的rhosts配合RSA演算方式来进行认证，只针对SSH v1
* `HostbasedAuthentication no` 这个项目与上面的项目类似，只针对SSH v2
* `IgnoreUserKnownHosts no`　 是否忽略`/home/.ssh/known_hosts`中记录的主机内容
* `PasswordAuthentication yes` 是否开启密码验证
* `PermitEmptyPasswords no`　 是否允许以空的密码登入
* `ChallengeResponseAuthentication yes` 挑战任何的密码认证，任何 login.conf规定的认证方式，均可适用！
* `PAMAuthenticationViaKbdInt yes` 是否启用其它的PAM模块！启用会导致 PasswordAuthentication设定失效 　

6. 与 Kerberos 有关的参数设定

* `KerberosAuthentication no`
* `KerberosOrLocalPasswd yes`
* `KerberosTicketCleanup yes`
* `KerberosTgtPassing no` 　

7. 底下是有关在X-Window底下使用的相关设定

* `X11Forwarding yes`
* `X11DisplayOffset 10`
* `X11UseLocalhost yes`

8. 登入后的项目

* `PrintMotd no` 登入后是否显示出一些信息，例如上次登入的时间、地点等等，默认是yes
* `PrintLastLog yes`　　显示上次登入的信息，默认是yes
* `KeepAlive yes`　 是否开启keepalive
* `UsePrivilegeSeparation yes` 使用者的权限设定项目
* `MaxStartups 10`　 同时允许尚未登入的联机会话数量（连上SSH但是尚未输入密码时，即联机会话）

9. 关于拒绝连接的设定项目

* `DenyUsers test` 设定拒绝登录的用户名称
* `DenyGroups test`　设定拒绝登录的用户群组

10. 关于 SFTP 服务的设定项目

* `Subsystem sftp /usr/lib/ssh/sftp-server`

### 5.3 known\_hosts

known\_hosts中存储是已认证的远程主机host key，每个SSH Server都有一个secret, unique ID, called a host key。

known\_hosts的存储路径：

* Linux: `~/.ssh/known_hosts`
* Windows:`C:\Users\uesr\.ssh\known_hosts`

当我们第一次通过SSH登录远程主机的时候，Client端会有如下提示：

```powershell
Host key not found from the list of known hosts.
Are you sure you want to continue connecting (yes/no)?
```

此时，如果我们选择yes，那么该host key就会被加入到Client的known\_hosts中，格式如下：

```config
# domain name+encryption algorithm+host key
example.hostname.com ssh-rsa AAAAB4NzaC1yc2EAAAABIwAAAQEA。。。
```

> 为什么需要known\_hosts？ 这个文件主要是通过Client和Server的双向认证，从而避免#中间人攻击，每次Client向Server发起连接的时候，不仅仅Server要验证Client的合法性，Client同样也需要验证Server的身份，SSH client就是通过known\_hosts中的host key来验证Server的身份的。
