# iptables

## 一、 防火墙知识

防火墙有两类：
1. 主机防火墙
  针对单个主机
2. 网路防火墙
  通常处于网络入口，针对网络入口进行防护，服务于局域网
  
通过对linux内核防火墙的配置便能够完成上面描述的防护功能。linux防火墙的配置用`iptables`工具来配置完成

## 二、 iptables
1. 什么是iptables
  iptables是netfilter项目的一部分，用于配置linux内核防火墙的命令。
  + iptables用于配置ipv4
  + ip6tables用于配置ipv6。
2. iptables解决什么问题
  iptables可以检测、修改、转发、重定向和丢弃ipv4的数据包。

3. 处理动作(actions)
处理动作在iptables中被称为target，动作分为基本动作和扩展动作。以下为常用的动作。
`ACCEPT`:允许数据包通过
`DROP`:直接丢失数据包，不给任何的回应信息
`REJECT`:拒绝数据包，会给请求方发送一个拒绝的信息
`SNAT`:源地址转换，解决网内用户用同一个公网地址上网的问题
`MASQUERAADE`:是SNAT的一种特殊形式，适用于动态的、临时会变的ip上
`DNAT`:目标地址转换
`REDIRECT`:在本机做端口映射
`LOG:`在/var/log/message文件中记录日志信息，然后将数据包传递给下一条规则，就是对数据除记录外不做任何其他的操作

4. 表（tables）
  iptables包含5张表：
  `raw` 用于配置数据包，raw中数据包不会被系统跟踪。
  `filter` 是用于存放所有与防火墙相关操作的默认表
  `nat` 用于网络地址转换（例如：端口转发）
  `mangle` 用于对特定数据包的修改
  `security` 用于强制访问控制网络规则（例如SElinux）
  大部分情况下仅需要使用filter和nat。
  规则表的先后顺序：`raw` -> `mangle` -> `nat` ->`filter`
  
5. 五链(chains)
  + PREROUTING: `[raw, nat, filter]`
  + INPUT: `[mangle, nat, filter]`
  + FORWARD: `[mangle, filter]`
  + OUTPUT: `[raw, mangle, nat, filter]`
  + POSTROUTING: `[mangle, nat, raw]`
  
6. 语法介绍
  ```shell
  iptables [-t table] chain command parameter [-j action]
  ```
  1). command
  
    | --- | --- |
    |参数	| 含义 |
    | -A	| 在指定链的末尾添加（append）一条新的规则 |
    | -D	| 删除（delete）指定链中的某一条规则，可以按规则序号和内容删除 |
    | -I	| 在指定链中插入（insert）一条新的规则，默认在第一行添加 |
    | -R	| 修改、替换（replace）指定链中的某一条规则，可以按规则序号和内容替换 |
    | -L	| 列出（list）指定链中所有的规则进行查看 |
    | -E	| 重命名用户定义的链，不改变链本身 |
    | -F	| 清空（flush） |
    | -N	| 新建（new-chain）一条用户自己定义的规则链 |
    | -X	| 删除指定表中用户自定义的规则链（delete-chain） |
    | -P	| 设置指定链的默认策略（policy） |
    | -Z	| 将所有表的所有链的字节和数据包计数器清零 |
    | -n	| 使用数字形式（numeric）显示输出结果 |
    | -v	| 查看规则表详细信息（verbose）的信息 |
    | -V	| 查看版本(version) |
    
   2). parameter
   
    | --- | --- | --- |
    |参数	| 取反 | 含义 |
    | `-p` | ! | 协议： TCP,UDP,ICMP,all |
    | `-s` | ! | 源主机地址 |
    | `-d` | ! | 目标主机地址 |
    | `-i` | ! | 网卡名 |
    | `-o` | ! | 网卡名+a |
    | `-sport` | ! | 源主机端口 |
    | `-dport` | ! | 目标主机端口 |
    
  
  7. 实例

  1). 清空当前的所有规则和计数
  ```shell
  iptables -F  # 清空所有的防火墙规则
  iptables -X  # 删除用户自定义的空链
  iptables -Z  # 清空计数
  ```
  
  2). 配置允许ssh端口连接
  ```shell
  iptables -A INPUT -s 192.168.1.0/24 -p tcp --dport 22 -j ACCEPT
  # 22为你的ssh端口， -s 192.168.1.0/24表示允许这个网段的机器来连接，其它网段的ip地址是登陆不了你的机器的。 -j ACCEPT表示接受这样的请求
  ```
  
  3). 允许本地回环地址可以正常使用
  ```shell
  iptables -A INPUT -i lo -j ACCEPT
  #本地圆环地址就是那个127.0.0.1，是本机上使用的,它进与出都设置为允许
  iptables -A OUTPUT -o lo -j ACCEPT
  ```
   
   4). 设置默认的规则
   ```shell
   iptables -P INPUT DROP # 配置默认的不让进
    iptables -P FORWARD DROP # 默认的不允许转发
    iptables -P OUTPUT ACCEPT # 默认的可以出去
   ```
   
   5). 配置白名单
   ```shell
    iptables -A INPUT -p all -s 192.168.1.0/24 -j ACCEPT  # 允许机房内网机器可以访问
    iptables -A INPUT -p all -s 192.168.140.0/24 -j ACCEPT  # 允许机房内网机器可以访问
    iptables -A INPUT -p tcp -s 183.121.3.7 --dport 3380 -j ACCEPT # 允许183.121.3.7访问本机的3380端口
   ```
   
   6). 开启相应的服务端口
    ```shell
    iptables -A INPUT -p tcp --dport 80 -j ACCEPT # 开启80端口，因为web对外都是这个端口
    iptables -A INPUT -p icmp --icmp-type 8 -j ACCEPT # 允许被ping
    iptables -A INPUT -m state --state ESTABLISHED,RELATED -j ACCEPT # 已经建立的连接得让它进来
    
    ```
    
   7). 保存规则到配置文件中
   ```shell
   cp /etc/sysconfig/iptables /etc/sysconfig/iptables.bak # 任何改动之前先备份，请保持这一优秀的习惯
    iptables-save > /etc/sysconfig/iptables
   ```
  8). 屏蔽IP
  ```shell
  iptables -A INPUT -p tcp -m tcp -s 192.168.0.8 -j DROP  # 屏蔽恶意主机（比如，192.168.0.8
  iptables -I INPUT -s 123.45.6.7 -j DROP       #屏蔽单个IP的命令
  iptables -I INPUT -s 123.0.0.0/8 -j DROP      #封整个段即从123.0.0.1到123.255.255.254的命令
  iptables -I INPUT -s 124.45.0.0/16 -j DROP    #封IP段即从123.45.0.1到123.45.255.254的命令
  iptables -I INPUT -s 123.45.6.0/24 -j DROP    #封IP段即从123.45.6.1到123.45.6.254的命令是
  ```
