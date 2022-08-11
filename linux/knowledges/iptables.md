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
    
    ps: 其它参数说明
    ```
    参数 -m limit --limit
    范例 iptables -A INPUT -m limit --limit 3/hour
    说明 用来匹配某段时间内封包的平均流量，上面的例子是用来匹配：每小时平均流量是否超过一次3个封包。除了每小时平均次外，也可以每秒钟、每分钟或每天平均一次，默认值为每小时平均一次，参数如后： /second、/minute、/day。 除了进行封包数量的匹配外，设定这个参数也会在条件达成时，暂停封包的匹配动作，以避免因骇客使用洪水攻击法，导致服务被阻断。

    参数 --limit-burst
    范例 iptables -A INPUT -m limit --limit-burst 5
    说明 用来匹配瞬间大量封包的数量，上面的例子是用来匹配一次同时涌入的封包是否超过 5个（这是默认值），超过此上限的封包将被直接丢弃。使用效果同上。
    ```
    
  
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

三、转发规则
略

四、ipset
> ipset是iptables的扩展,它允许你创建匹配整个地址集合的规则。 而不像普通的iptables链只能单IP匹配, ip集合存储在带索引的数据结构中,这种结构即时集合比较大也可以进行高效的查找，除了一些常用的情况,比如阻止一些危险主机访问本机，从而减少系统资源占用或网络拥塞,IPsets也具备一些新防火墙设计方法,并简化了配置

1.ipset安装

yum安装： yum install ipset
2.创建一个ipset

ipset create xxx hash:net （也可以是hash:ip ，这指的是单个ip,xxx是ipset名称）
ipset默认可以存储65536个元素，使用maxelem指定数量

ipset create blacklist hash:net maxelem 1000000 #黑名单
ipset create whitelist hash:net maxelem 1000000 #白名单
查看已创建的ipset

ipset list
3.加入一个名单ip

ipset add blacklist 10.60.10.xx
4.去除名单ip

ipset del blacklist 10.60.10.xx
5.创建防火墙规则，与此同时，allset这个IP集里的ip都无法访问80端口（如：CC攻击可用）

iptables -I INPUT -m set –match-set blacklist src -p tcp -j DROP
iptables -I INPUT -m set –match-set whitelist src -p tcp -j DROP
service iptables save
iptables -I INPUT -m set –match-set setname src -p tcp –destination-port 80 -j DROP
6.将ipset规则保存到文件

ipset save blacklist -f blacklist.txt
ipset save whitelist -f whitelist.txt
7.删除ipset

ipset destroy blacklist
ipset destroy whitelist
8.导入ipset规则

ipset restore -f blacklist.txt
ipset restore -f whitelist.txt
ipset的一个优势是集合可以动态的修改，即使ipset的iptables规则目前已经启动，新加的入ipset的ip也生效
