# 网络设置

***

## ifconfig 命令临时配置IP地址

* linux系统对网卡命名规范
	例如当前网卡名为enp3s0:
	```
	1. en代表以太网
	2. p3s0代表PCI接口的物理位置为(3,0),其中横坐标代表bus，纵坐标代表slot
	```
  > 在之前版本（或其它版本linux系统）中网卡命名的方式为 eth0，eth1，对应着第一个网卡和第二个网卡。

* 查看网络配置信息

  ```
  ifconfig [interface] //interface对应网络名，
  ```

  例如：

  ```linux
  ifconfig enp3s0
  [root@localhost ~]# ifconfig enp3s0
  enp3s0: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 1500
          inet 192.168.254.107  netmask 255.255.255.0  broadcast 192.168.254.255
          inet6 fe80::dd21:eb6e:b644:efd5  prefixlen 64  scopeid 0x20<link>
          ether 18:31:bf:6a:95:6b  txqueuelen 1000  (Ethernet)
          RX packets 8992  bytes 12021014 (11.4 MiB)
          RX errors 0  dropped 0  overruns 0  frame 0
          TX packets 4379  bytes 322855 (315.2 KiB)
          TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0
  第一行：MTU:1500（最大传输单元）：1500字节 UP（代表网卡开启状态）RUNNING（代表网卡的网线被接上）MULTICAST（支持组播）
  第二行：网卡的IP地址()inet、掩码(netmask) 广播地址（broadcast）
  第三行：ether（mac地址）
  第四、六行：接收、发送数据包情况统计
  第五、七行：接收、发送数据字节数统计信息。
  ```

* 临时配置IP
  ifconfig <netname> IP  netmask 255.255.255.0

  ```
  ifconfig enp3s0 192.168.254.107 netmask 255.255.255.0
  //添加多个IP
  ifconfig enp3s0:0 192.168.254.123 netmask 255.255.255.0
  ```

  

* 修改mac地址

  ```
  ifconfig enp3s0 hw ether xx：xx：xx：xx：xx：xx
  ```

* 管理网卡

  ```
  ifconfig enp3s0 down  //禁用网卡
  ifconfig enp3s0 up ///启用网卡
  ```

## IP命令

IP命令时对ifconfig的替代。

> IP 对象<addr | link | route> [option]
>
> ```
> # ip link show                           # 显示网络接口信息
> # ip link set eth0 upi                   # 开启网卡
> # ip link set eth0 down                  # 关闭网卡
> # ip link set eth0 promisc on            # 开启网卡的混合模式
> # ip link set eth0 promisc offi          # 关闭网卡的混个模式
> # ip link set eth0 txqueuelen 1200       # 设置网卡队列长度
> # ip link set eth0 mtu 1400              # 设置网卡最大传输单元
> 
> # ip addr show                           # 显示网卡IP信息
> # ip addr add 192.168.0.1/24 dev eth0    # 设置eth0网卡IP地址192.168.0.1
> # ip addr del 192.168.0.1/24 dev eth0    # 删除eth0网卡IP地址
> 
> # ip route list                          # 查看路由信息
> # ip route add 192.168.4.0/24  via  192.168.0.254 dev eth0 # 设置192.168.4.0网段的网关为192.168.0.254,数据走eth0接口
> # ip route add default via  192.168.0.254  dev eth0        # 设置默认网关为192.168.0.254
> # ip route del 192.168.4.0/24                              # 删除192.168.4.0网段的网关
> # ip route del default                                     # 删除默认路由
> ```
>
> 

## 网络配置

配置文件地址 **/etc/sysconfig/network-scripts**

如果不确定网络配置文件的位置，可以通过find命令结合已有网卡名进行查找

> linux> find /etc -name *enp3s0
>
> /etc/sysconfig/network-scripts/ifcfg-enp3s0

查看当前网卡的配置文件

> [root@localhost ~]# cat /etc/sysconfig/network-scripts/ifcfg-enp3s0 
>
> TYPE=Ethernet             #网络类型，以太网
> PROXY_METHOD=none 
> BROWSER_ONLY=no
> BOOTPROTO=dhcp
> DEFROUTE=yes
> IPV4_FAILURE_FATAL=no
> IPV6INIT=yes
> IPV6_AUTOCONF=yes
> IPV6_DEFROUTE=yes
> IPV6_FAILURE_FATAL=no
> IPV6_ADDR_GEN_MODE=stable-privacy
> NAME=enp3s0  //网络名称
> UUID=3c52c2bc-12d9-435f-a5e3-288e4af2f2b4
> DEVICE=enp3s0 
> ONBOOT=yes //系统启动时启动网卡
> IPADDR0=192.168.254.123 //静态IP地址
> GATEWAY0=192.168.254.254 //网关
> PREFIXO0=24  //掩码，子网掩码自行google
> DNS1=192.168.254.254 //DNS服务器
> \#BOOTPROTO=dhcp  //如果不设置静态IP，这个注释要取消，自动分配IP
>
> 可以添加多个IP，只需要将下面内容添加到文件末尾即可
>
> IPADDR1=192.168.254.123 //静态IP地址
> GATEWAY1=192.168.254.254 //网关
> PREFIXO1=24  //掩码，子网掩码自行google

配置文件修改完毕后，可以使用shutdown -r或ifdown ifup来重启电脑或网卡，配置内容才能生效

## 网卡启动关闭

1. ifdown 和 ifup启动和关闭网卡
   这两个程序其实是script而已，它会直接到 /etc/ sysconfig/network-scripts目录下搜索对应的配置文件，例如ifup eth0，它会找出ifcfg-eth0这个文件的内容，然后加以设置。如果对应配置文件不存在，则会失败
   另外，如果以ifconfig eth0来设置或者是修改了网络接口后，就无法再以ifdown eth0的方式来关闭了。因为ifdown会分析比较目前的网络参数与ifcfg-eth0是否相符，不符的话，就会放弃这次操作
2. **/etc/init.d/network restart** 重启网卡
3. **service network restart**

## 如何网卡开机启动

* 方式一：将脚本"ifconfig <interface> up" 放到启动项目中
* 方式二：修改配置文件cfg-enp3s0中的onboot = yes；