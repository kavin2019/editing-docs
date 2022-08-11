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

3. 处理动作
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
  
5. 五链
  + PREROUTING: `[raw, nat, filter]`
  + INPUT: `[mangle, nat, filter]`
  + FORWARD: `[mangle, filter]`
  + OUTPUT: `[raw, mangle, nat, filter]`
  + POSTROUTING: `[mangle, nat, raw]`
  
6. 语法介绍
  ```shell
  iptables [-t table] chain command parameter [action]
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
    ｜ `-p` | ! |   |
