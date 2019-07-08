## web服务压力测试工具siege

### 简介

Siege是一个多线程http负载测试和基准测试工具，可以根据配置对一个WEB站点进行多用户的并发访问，记录每个用户所有请求过程的相应时间，并在一定数量的并发访问下重复进行

### 安装

1. 下载。[官网下载](http://www.joedog.org)

2. 选择版本（安装版本`http://download.joedog.org/siege/siege-4.0.4.tar.gz`）

3. 下载

   ```shell
   $ curl http://download.joedog.org/siege/siege-4.0.4.tar.gz > siege-4.0.4.tar.gz
   #解压缩
   $ tar -zxvf siege-4.0.4.tar.gz
   ```

   

4. 执行./configure

   ```shell
   $ cd siege-4.0.4
   $ .configure --prefix=/usr/local/siege
   ```

5. 安装

   ```shell
   $ make && make install
   ```

PS:安装前需要确保系统已经安装了GCC。



### 格式

```shell
$ siege [OPTIONS] url
```

#### OPTIONS

```shell
  -V, --version             查看版本
  -h, --help                查看帮助
  -C, --config              查看当前的配置。（默认位置 PREFIX/etc/siegerc)
  -v, --verbose             测试时显示详细信息
  -q, --quiet               QUIET turns verbose off and suppresses output.
  -g, --get                 GET, pull down HTTP headers and display the
                            transaction. Great for application debugging.
  -p, --print               PRINT, like GET only it prints the entire page.
  -c, --concurrent=NUM      并发用户数量，默认10。对应.siegerc配置文件中的CONCURRENT
  -r, --reps=NUM            (repetitions)，重复数量，即每个连接发出的请求数量，设置这个的话，就不需要设置-t了。对应.siegerc配置文件中的reps = x指令
  -t, --time=NUMm           TIMED 测试持续时间，可以以S, M, or H结尾，表示秒，分和小时。
                            ex: --time=1H, 持续测试一小时.
  -d, --delay=NUM           Time DELAY, 每个请求间的间隔时间（在0-NUM之间的随机数）
  -b, --benchmark           BENCHMARK: 如果这个参数为true，那么表示-d无效，请求间隔为0.
  -i, --internet            INTERNET user simulation, hits URLs randomly.
  -f, --file=FILE           FILE, 指定一个特殊的文件，该文件内存储了需要测试的url地址。这个用于压力测试不仅只有少数地址的时候使用。如果不指定，默认：PREFIX/var/urls.txt。
  -R, --rc=FILE             RC, 指定siege的配置文件。默认时.siegerc配置文件
  -l, --log[=FILE]          LOG to FILE. 记录到日志中，如果不指定文件，默认使用: PREFIX/var/siege.log
  -m, --mark="text"         MARK, mark the log file with a string.
                            between .001 and NUM. (NOT COUNTED IN STATS)
  -H, --header="text"       Add a header to request (can be many)
  -A, --user-agent="text"   Sets User-Agent in request。设置请求头中的User-Agent
  -T, --content-type="text" Sets Content-Type in request。设置请求头中的Content-Type
      --no-parser           NO PARSER, turn off the HTML page parser
      --no-follow           NO FOLLOW, do not follow HTTP redirects
```

### 范例

```shell
$ siege -c 10 -r 10  172.17.0.2:80
Transactions:		         100 hits #执行了100次请求。10*10
Availability:		      100.00 % #100%的请求成功
Elapsed time:		        0.05 secs #100次请求耗时
Data transferred:	        0.00 MB # 传输数据量大小
Response time:		        0.00 secs #响应时间
Transaction rate:	     2000.00 trans/sec #每秒响应请求数量
Throughput:		        0.02 MB/sec #每秒的吞吐量
Concurrency:		        6.20 #实际并发数
Successful transactions:         100 #成功传输次数
Failed transactions:	           0 #失败传输次数
Longest transaction:	        0.02 # 最长响应的时间
Shortest transaction:	        0.00 #最短的响应时间
```





### 其它工具

webbench、ab（apache自带的压测工具

）、http_load