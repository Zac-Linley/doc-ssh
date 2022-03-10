## 1.1 SSH协议框架中的三个子协议

- **传输层协议​​SSH-TRANS​​**

  提供服务器验证、完整性和保密性功能,建立在传统的TCP/IP协议之上。

- **验证协议​​SSH-USERAUT**
  
  向服务器验证客户端用户，有基于用户名密码和公钥两种验证方式，建立在传输层协议​​SSH-TRANS​​之上。

- **连接协议​​SSH-CONNECT**
  
  将加密隧道复用为若干逻辑信道。它建立在验证协议之上。

<img src="https://linley.oss-cn-shanghai.aliyuncs.com/typora_image/howitworks2022-03-10-23-45-47.png" alt="howitworks2022-03-10-23-45-47" width="300" height="">

## 1.2 SSH工作流程

<img src="https://linley.oss-cn-shanghai.aliyuncs.com/typora_image/howitworks2022-03-10-23-46-09.png" alt="howitworks2022-03-10-23-46-09" width="400" height="">

## 1.3 通过Wireshark抓包分析各个阶段

### 1.3.1 TCP三次握手

<img src="https://linley.oss-cn-shanghai.aliyuncs.com/typora_image/howitworks2022-03-10-23-46-27.png" alt="howitworks2022-03-10-23-46-27" width="" height="">

SSH服务器端在[22端口](md/guide.md#sshport)侦听客户端的连接请求，接收到客户端的连接建立请求后，与客户端进行三次握手，建立起一条TCP连接，后续的所有报文交互都在这个TCP连接之上进行。

### 1.3.2 版本协商

<img src="https://linley.oss-cn-shanghai.aliyuncs.com/typora_image/howitworks2022-03-10-23-46-44.png" alt="howitworks2022-03-10-23-46-44" width="" height="">

服务器和客户端都会向对端发送自己支持的版本号。服务器端和客户端收到对端发送过来的版本后，与本端的版本号进行比较，双方都支持的最高版本号即为协商出的版本号。

### 1.3.3 算法协商

<img src="https://linley.oss-cn-shanghai.aliyuncs.com/typora_image/howitworks2022-03-10-23-47-01.png" alt="howitworks2022-03-10-23-47-01" width="" height="">

- 客户端和服务器端都将自己支持的算法列表发送给对方；
- 双方依次协商每一种算法（密钥交换算法、加密算法等）。每种算法的协商过程均为：从客户端的算法列表中取出第一个算法，在服务器端的列表中查找相应的算法，如果匹配上相同的算法，则该算法协商成功；否则继续从客户端算法列表中取出下一个算法，在服务器端的算法列表中匹配，直到匹配成功。如果客户端支持的算法全部匹配失败，则该算法协商失败。
- 某一种算法协商成功后，继续按照上述方法协商其他的算法，直到所有算法都协商成功；如果某一种算法协商失败，则客户端和服务器之间的算法协商失败，服务器断开与客户端的连接。

在`algorithms`包含了所有需要协商的算法

| NO. | 描述 | 解释 |
| --- | --- | --- |
| 1 | `kex_algorithms` | 密钥交换算法Key Exchange，里边包含了D-H算法，用于生成会话密钥 |
| 2 | `server_host_key_algorithms` | 服务器主机密钥算法Host Key，用于生成密钥对 |
| 3 | `encryption_algorithms_client_to_server` | 对称加密算法，常用的有`aes128-cbc,3des-cbc` |
| 4 | `mac_algorithms_client_to_server` | Mac算法，主要用于保证数据完整性 |
| 5 | `compression_algorithms_client_to_server` | 压缩算法 |

### 1.3.4 密钥交换

<img src="https://linley.oss-cn-shanghai.aliyuncs.com/typora_image/howitworks2022-03-10-23-47-18.png" alt="howitworks2022-03-10-23-47-18" width="" height="">

客户端生成一个临时密钥ephemera- public key发送给服务器端【？目前还没搞明白为什么？】

<img src="https://linley.oss-cn-shanghai.aliyuncs.com/typora_image/howitworks2022-03-10-23-47-29.png" alt="howitworks2022-03-10-23-47-29" width="" height="">

服务端与客户端使用Diffie-Hellman算法生成会话密钥Session key。

在这个包中，服务器端将主机密钥Host Key、会话密钥Session key、签名等一同发送给客户端。

<img src="https://linley.oss-cn-shanghai.aliyuncs.com/typora_image/howitworks2022-03-10-23-47-38.png" alt="howitworks2022-03-10-23-47-38" width="" height="">

客户端回复一个New Key，接下来进入加密通信。

<img src="https://linley.oss-cn-shanghai.aliyuncs.com/typora_image/howitworks2022-03-10-23-47-47.png" alt="howitworks2022-03-10-23-47-47" width="" height="">

接下来的会话过程全程加密，从抓包中已经看不到任何铭文信息了。

### 1.3.5 用户认证

1. 客户端向服务器端发送认证请求报文，其中携带的认证方式为“none”。
2. 服务器收到none方式认证请求，回复认证挑战报文，其中携带服务器支持、且需要该用户完成的认证方式列表。
3. 客户端从服务器发送给自己的认证方式列表中选择某种认证方式，向服务器发起认证请求，认证请求中包含用户名、认证方法、与该认证方法相关的内容：
   - 密码认证方式中，内容为用户的密码；
   - 公钥认证方式中，内容为用户本地密钥对的公钥部分（公钥验证阶段）或者数字签名（数字签名验证阶段）。
4. 服务器接收到客户端的认证请求，验证客户端的认证信息：
   - 密码认证方式：服务器将客户端发送的用户名和密码信息，与设备上或者远程认证服务器上保存的用户名和密码进行比较，从而判断认证成功或失败。
   - 公钥认证方式：服务器对公钥进行合法性检查，如果不合法，则认证失败；否则，服务器利用数字签名对客户端进行认证，从而判断认证成功或失败。
5. 服务器根据本端上该用户的配置，以及用户认证的完成情况，决定是否需要客户端继续认证，分为以下几种情况：
   - 如果该种认证方式认证成功，且该用户不需要继续完成其他认证，则服务器回复认证成功消息，认证过程顺利完成。
   - 如果该种认证方式认证成功，但该用户还需要继续完成其他认证，则回复认证失败消息，继续向客户端发出认证挑战，在报文中携带服务器需要客户端继续完成的认证方式列表；
   - 如果该种认证方式认证失败，用户的认证次数尚未到达认证次数的最大值，服务器继续向客户端发送认证挑战；
   - 如果该种认证方式认证失败，且用户的认证次数达到认证次数的最大值，用户认证失败，服务器断开和客户端之间的连接。

### 1.3.6 服务请求

SSH协议支持多种应用服务。用户成功完成认证后，SSH客户端向服务器端发起服务请求，请求服务器提供某种应用。

服务请求的过程为：
1. 客户端发送`SSH_MSG_CHANNEL_OPEN`消息，请求与服务器建立会话通道session；
2. 服务器端收到`SSH_MSG_CHANNEL_OPEN`消息后，如果支持该通道类型，则回复`SSH_MSG_CHANNEL_OPEN_CONFIRMATION`消息，从而建立会话通道；
3. 会话通道建立之后，客户端可以申请在通道上进行shell或subsystem类型的会话，分别对应SSH和SFTP两种类型的服务。