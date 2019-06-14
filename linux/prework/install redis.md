



#  安装REDIS

***

环境：linux centos 7.6

* 下载redis安装源代码

  ````
  curl http://download.redis.io/releases/redis-5.0.4.tar.gz
  ````

  下载完成后，文件会保存到当前路径：

  ```
  [admin@localhost ~]$ curl -O http://download.redis.io/releases/redis-5.0.4.tar.gz
    % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                   Dload  Upload   Total   Spent    Left  Speed
  100 1920k  100 1920k    0     0   721k      0  0:00:02  0:00:02 --:--:--  721k
  [admin@localhost ~]$ ls
  redis-5.0.4.tar.gz
  ```

  

* 解压包

  ```
  [admin@localhost ~]$ tar -zxvf redis-5.0.4.tar.gz
  [admin@localhost ~]$ ls
  redis-5.0.4.tar.gz redis-5.0.4
  ```

  解压后在当前录下有个redis-5.0.4的文件夹

* 编译
  进入到文件夹redis-5.0.4中，输入命令make

  ```
  [admin@localhost ~]$ cd redis-5.0.4
  [admin@localhost redis-5.0.4]$ make
  ```

   编译过程中可能会出现一下错误
  >
  > ```
  > /bin/sh: cc: command not found
  > make[1]: *** [adlist.o] Error 127
  > ```
  > 解决方案：系统找不到cc命令，这个命令是GCC编译命令，所以即有可能是没有安装GCC，按装下GCC即可。详见[centos7安装GCC](centos7ofGCC.md)
  >
  >
  > ```
  > zmalloc.h:50:31: fatal error: jemalloc/jemalloc.h: No such file or directory
  > make[1]: *** [adlist.o] Error 1
  > ```
  >
  > 解决方案：原因是jemalloc重载了Linux下的ANSI C的malloc和free函数。解决办法：make时添加参数。
  >
  > > make MALLOC=libc
  编译完成后，会提示你运行 `make test`,忽略不管即可


* 安装
  进入到src文件夹，运行命令make install
  ```shell
  [root@localhost src]# make install //需要root权限，sudo也可以
  ```
  命令运行完表示安装完成。make install会将应用到连接创建到/usr/local/bin中
  ```shell
  [admin@localhost ~]$ ls /usr/local/bin/
redis-benchmark  redis-check-aof  redis-check-rdb  redis-cli  redis-sentinel  redis-server
  ```
  
* 配置redis
  如果直接运行redis-server会得到提示：

  ```
  Warning: no config file specified, using the default config. In order to specify a config file use redis-server /path/to/redis.conf
  ```

  提示没有使用配置文件，按默认配置启动。

  redis的配置文件'redis.config'在根目录下;修改一下配置：

  ```
  # bind 127.0.0.1  //禁用这一段
  protected-mode no  //关闭保护模式
  daemonize true //以守护进程启动。启动后就作为后台程序运行。关闭连接也会运行。
  ```

* 查看redis运行状态

  ```
  [root@localhost ~]# ps -ef | grep redis
  root     15349     1  0 13:25 ?        00:00:05 redis-server *:6379 //可以查到redis使用6379端口
  root     15389 15128  0 14:49 pts/0    00:00:00 grep --color=auto redis
  ```

  

* 运行时问题

  1. connect refused

     ```
     redis connection refused: connect无法连接redis
     解决方案：
     1、Redis.conf配置文件中 注释掉 bind 127.0.0.1
     2、防火墙关闭（或添加可访问的端口，具体不在此描述）
     ```

  2. 如果运行时提示端口被占用，可以查看端口是那个应用占用了

     ```
     [root@localhost ~]# netstat -lnp|grep 6379
     tcp   0   0 0.0.0.0:6379     0.0.0.0:*     LISTEN      15349/redis-server  
     tcp6  0   0 :::6379          :::*          LISTEN      15349/redis-server 
     ```

     或者(推荐这个)

     ```
     [root@localhost ~]# ss -lnp |grep redis
     tcp  LISTEN   0   128    *:6379       *:*      users:(("redis-server",pid=15349,fd=7))
     tcp  LISTEN   0   128    :::6379      :::*     users:(("redis-server",pid=15349,fd=6))
     ```

     