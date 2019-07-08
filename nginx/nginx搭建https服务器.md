## 搭建https服务器

### 简介

> TSL是传输层安全协议(Transport Secure Protocol）的简称，它的前身是SSL(Secure Sockets Layer)。TLS的设计目标是为互联网通信提供安全保证，防止数据被窃听，篡改。TLS协议由记录层(TLS Record Layer)和握手层(TLS Handshake Layer)组成，记录层处于协议的最低层，为TLS协议提供安全可靠的连接。TLS在协议栈中处于HTTP和TCP协议之间，但是TLS协议是独立于应用层协议的，也就是说高层的应用层协议，如HTTP，FTP等都可以创建在TLS之上，TLS的下层可以是任何可靠传输层协议，如TCP，SPX等。

  关于SSL和TLS机制的具体细节可以参考阮一峰的[《SSL/TLS协议运行机制的概述》](http://www.ruanyifeng.com/blog/2014/02/ssl_tls.html)。

TSL又分为两个部分：

* 记录层协议**(TLS Record Protocol)**
  记录层协议负责将要发出的消息进行分片，压缩，打上消息认证码(MAC)并最终交到传输层；或将接收到的数据进行解压，解密，数据校验，重新 组装后传到上一层
* 握手层协议**(TLS Handshake Protocol)**
  握手层协议处于记录层协议之上，握手层协议的作用是在真正的应用数据传输之前，使客户端和服务器互相进行身份认证，协商加密算法以及生成加密密钥。握手层用于协商出记录层的参数。这些参数将会被记录层用于双方的身份认证，实例化协商的安全参数，错误报告

TSL在协议栈中的位置：
![](/Users/james/Documents/docs/github/markdown/images/tsl.png)

TLS握手过程：

> 1. 客户端发送ClientHello消息，包含可选密码组件，一个客户端生成的随机数等，服务端接收后回一个ServerHello消息给客户端，确定密码方案，并生成一个服务器随机数，发给客户端。
>     这一步的数据传输在TCP上进行，数据都是公开的，中间人攻击者可以很轻松的得到这些数据。
>
> 2. 服务器发送证书，服务器的身份确认成功后；服务器可能会请求一个客户端证书，要求客户端进行身份认证，客户端给服务器发出证书，验明身份。这一步不是必须的。
>
> 3. 客户端向服务器发出摘要认证，确认接收的消息的完整性，保证没有被第三方篡改。
>
> 4. 双方交换必需的加密参数。其中包括一个预主密钥，客户端会用服务器证书中的公钥将这个密钥加密传给服务器，服务器使用私钥进行解密。
>
> 5. 客户端生成一个预主密钥，并使用服务器证书中的公钥加密，服务器解密后生成一个主密钥。双方使用这个主密钥(master secret)对应用层数据进行对称加密(如AES256)。 主密钥是客户端和服务器共享的。
>
> 6. 最后，把这些准备好的的安全参数交给记录层协议

会话恢复过程：

客户端服务器出于性能的考虑，可能会恢复前面的会话，或复制一个当前存在的会话，而不是重新协商密码参数，建立新的会话(后续可以使用tcpdump抓包进行跟踪，发现这个过程确实是耗时占比很大)。

> 1. 客户端发出一个ClientHello消息，这个消息包含要恢复的会话的Session ID。
>
> 2. 服务接到这个SessionID后，检查它的缓存是否存在该会话ID。如果找到了，服务器将使用该会话状态重建连接，并发一个ServerHello给客户端，其中包含一个和ClientHello消息一模一样的SessionID。
>
> 3. 此时，协议规定，必须要双方发出ChangeCipherSpec消息，并接着发出Finished消息确认握手完成。然后客户端服务器就可以传输应用层数据了。
>
> 4. 如果服务器没有在缓存中找到Session ID，那么新的SessionID将会建立，然后又会执行一次完全握手。

### 搭建过程

1. 准备环境
   安装nginx，并且在安装时需要注意进行./congfiure配置时需要指定`--with-http_ssl_module`模块，否则会提示nginx没有安装该模块，而无法启动或检验配置出错。如果已经安装nginx而没有指定，可以通过[启动nginx的ssh功能](https://www.cnblogs.com/piscesLoveCc/p/6120875.html)来解决。

2. 配置项详解

   * 配置项：

     ```shell
       server{
         ...
         #为一个server开启ssl支持  或者在监听端口时指定，例如；listen 80 ssl;
         ssl          on;  
         #为虚拟主机指定pem格式的证书文件  
         ssl_certificate    /home/wangzhengyi/ssl/wangzhengyi.crt;   
         #为虚拟主机指定私钥文件  
         ssl_certificate_key  /home/wangzhengyi/ssl/wangzhengyi_nopass.key;   
         #客户端能够重复使用存储在缓存中的会话参数时间 (在会话缓存期间，可以恢复会话达到提高性能的目的) 
         ssl_session_timeout  5m;  
         #指定使用的ssl协议   
         ssl_protocols  SSLv3 TLSv1;   
         #指定许可的密码描述  
         ssl_ciphers  ALL:!ADH:!EXPORT56:RC4+RSA:+HIGH:+MEDIUM:+LOW:+SSLv2:+EXP;   
         #SSLv3和TLSv1协议的服务器密码需求优先级高于客户端密码  
         ssl_prefer_server_ciphers   on;   
         ...
       }
     ```

   * 作用域：server

3. 搭建https服务器

   * 搭建一个http服务器（[参考nginx搭建web服务器](nginx搭建web服务器.md))

   * 使用openssl生成服务器的私钥server.key以及server.csr文件

     > - CSR：Cerificate Signing Request，证书签署请求文件，里面包含申请者的 DN（Distinguished Name，标识名）和公钥信息，**在第三方证书颁发机构签署证书的时候需要提供**。证书颁发机构拿到 CSR 后使用其根证书私钥对证书进行加密并生成 CRT 证书文件，里面包含证书加密信息以及申请者的 DN 及公钥信息
     > - Key：证书申请者私钥文件，和证书里面的公钥配对使用，在 HTTPS 『握手』通讯过程需要使用私钥去解密客戶端发來的经过证书公钥加密的随机数信息，是 HTTPS 加密通讯过程非常重要的文件，**在配置 HTTPS 的時候要用到**

     ```sh l lsh l
     #生成服务端的私钥
     $ openssl genrsa -out server.key 2048
     #利用私钥生成csr
     $opensll req -new -key  server.key -out server.csr
     ```

   * 生成CA的证书

     ```shell
     #生成CA的私钥
     $ openssl genrsa -out ca.key 2048
     #生成CA的csr
     $ openssl req -new -key ca.key -out ca.csr
     #生成CA证书
     $ openssl x509 -req -in ca.csr -signkey ca.key -out  ca.crt
     ```

   * 利用自己生成的CA证书生成服务端的证书

     ```shell
     $ openssl x509 -new -CA ca.crt -CAkey ca.key -CAcreateserial  -out server.crt
     ```

   * 配置nginx（其它部分省略）

     ```shell
      server {
             listen 80 ;
             listen 8088 ssl;  # 设置两个监听端口，一个支持http一个支持https。用于后续压测
             server_name  localhost;
     
             charset utf-8;
             ssl_certificate     ../ssl/server.crt;
             ssl_certificate_key ../ssl/server.key;
             ssl_ciphers  HIGH:!aNULL:!MD5;
     
             location / {
                 root   /var/html;
                 index  index.html index.htm;
             }
     }
     ```

   * 检测配置并重启nginx

     ```shell
     $ nginx -t
     $ nginx -s reload
     ```

   * 在浏览其中输入 `https://localhsot:8088`
   	
   	因为证书是我们自己签发的，签发机构是伪造的，不在浏览器的信任列表中。主流浏览器都会发出警告，然后提供一种忽略并继续访问的方式，在实验中我们可以选择这么做。实际中千万不要忽略浏览器的警告，轻易相信不明来源的证书。忽略警告后的结果：
   	
   	```shell
   	welcome!
   	```
   	
   * 

4. 