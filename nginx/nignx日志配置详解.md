## 详解nginx日志配置

### 简介

nginx在运行时提供日志功能，用于记录请求以及运行时发生的错误信息。记录日志的内容有利于日后对web服务的故障检测和调试有很大的帮助。

ningx日志分为以下几种类型：

1. error_log:错误日志
2. access_log:访问日志
3. rewrite_log:重写日志

nginx有一个非常灵活日志系统，可以随意配置日志格式。

### access_log

访问日志主要是记录客户端访问nginx的请求，客户端向Nginx服务器发起的每一次请求都记录在这里。客户端IP，浏览器信息，referer，请求处理时间，请求URL等都可以在访问日志中得到。当然具体要记录哪些信息，你可以通过`log_format`指令定义

* 配置语法：

  ```shell
  access_log path [format [buffer=size] [gzip[=level]] [flush=time] [if=condition]]; # 设置访问日志
  access_log off; # 关闭访问日志

  path:日志文件路径
  format:指定日志保存的格式log_format，默认是combine，系统自定义的格式。
  buffer:指定缓存大小
  gzip：压缩级别
  flush:每隔多久将缓存中的数据保存到日志文件
  if:条件判断。如果指定的条件计算为0或空字符串，那么该请求不会写入日志

  off:关闭访问日志
  ```

* 作用域：http,server,location,limit_except。如果超出了这些作用域，那么nginx会报错。

* 使用log_formater自定日志格式
  nginx自定义了一个名为combine的日志格式：

  ```shell
  log_format combined '$remote_addr - $remote_user [$time_local] '
                      '"$request" $status $body_bytes_sent '
                      '"$http_referer" "$http_user_agent"';
  ```

  如果想要自定义格式可以通过log_format指令来实现

  语法：

  ```shell
  log_format name [escape=default|json] string [string...];
  
  name: access_log可以引用的名称
  escape：设置变量中的字符编码方式，json还是default。默认是default
  string：要定义的日志格式，该参数可以有多个，用空格隔开；可以使用nginx变量。
  ```

  下面是`log_format`指令中常用的一些变量：

  | 变量                  | 含义                                                         |
  | :-------------------- | :----------------------------------------------------------- |
  | $bytes_sent           | 发送给客户端的总字节数                                       |
  | $body_bytes_sent      | 发送给客户端的字节数，不包括响应头的大小                     |
  | $connection           | 连接序列号                                                   |
  | $connection_requests  | 当前通过连接发出的请求数量                                   |
  | $msec                 | 日志写入时间，单位为秒，精度是毫秒                           |
  | $pipe                 | 如果请求是通过http流水线发送，则其值为"p"，否则为“."         |
  | $request_length       | 请求长度（包括请求行，请求头和请求体）                       |
  | $request_time         | 请求处理时长，单位为秒，精度为毫秒，从读入客户端的第一个字节开始，直到把最后一个字符发送张客户端进行日志写入为止 |
  | $status               | 响应状态码                                                   |
  | $time_iso8601         | 标准格式的本地时间,形如“2017-05-24T18:31:27+08:00”           |
  | $time_local           | 通用日志格式下的本地时间，如"24/May/2017:18:31:27 +0800"     |
  | $http_referer         | 请求的referer地址。                                          |
  | $http_user_agent      | 客户端浏览器信息。                                           |
  | $remote_addr          | 客户端IP                                                     |
  | $http_x_forwarded_for | 当前端有代理服务器时，设置web节点记录客户端地址的配置，此参数生效的前提是代理服务器也要进行相关的x_forwarded_for设置。 |
  | $request              | 完整的原始请求行，如 "GET / HTTP/1.1"                        |
  | $remote_user          | 客户端用户名称，针对启用了用户认证的请求                     |
  | $request_uri          | 完整的请求地址，如 "https://daojia.com/"                     |

* access_log的用法：
  基本用法

  ```shell
  access_log /var/logs/nginx-access.log
  #使用默认的log_format，'combine'
  ```

  自定义格式内容：

  ```shell
  access_log /var/logs/nginx-access.log main
  log_format  main  '$remote_addr - $remote_user [$time_local] "$request" '
                    '$status $body_bytes_sent "$http_referer" '
                    '"$http_user_agent" "$http_x_forwarded_for"';
                    
  #nginx的访问日志记录：
  112.195.209.90 - - [20/Feb/2018:12:12:14 +0800] "GET / HTTP/1.1" 200 190 "-" "Mozilla/5.0 (Linux; Android 6.0; Nexus 5 Build/MRA58N) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/63.0.3239.132 Mobile Safari/537.36" "-"
  ```

  

### error_log

错误日志，用于记录nginx服务器和请求过程中发生的错误

* 语法：

  ```shell
  error_log path [level]
  
  path:错误日志文件路径
  level：日志级别。总共有`debug, info, notice, warn, error, crit, alert,emerg`8个级别，级别大小依次递增，设置level后，大于level的所有级别都会写入日志文件。默认error；
  ```

* 作用域 `main`， `http`, `mail`, `stream`, `server`, location

* 基本用法：

  ```shell
  error_log /var/logs/error.log
  #默认level为error
  ```

### rewrite_log

由ngx_http_rewrite_module模块提供的。用来记录重写日志的。对于调试重写规则建议开启。 
语法: rewrite_log on | off;
默认值: rewrite_log off;
作用域: http, server, location, if
启用时将在error log中记录notice级别的重写日志。

### open_log_file_cache

每一条日志记录的写入都是先打开文件再写入记录，然后关闭日志文件。如果你的日志文件路径中使用了变量，如`access_log /var/logs/$host/nginx-access.log`，为提高性能，可以使用`open_log_file_cache`指令设置日志文件描述符的缓存。

语法：

```shell
open_log_file_cache max=N [inactive=time] [min_uses=N] [valid=time];

max 设置缓存中最多容纳的文件描述符数量，如果被占满，采用LRU算法将描述符关闭。
inactive 设置缓存存活时间，默认是10s。
min_uses 在inactive时间段内，日志文件最少使用几次，该日志文件描述符记入缓存，默认是1次。
valid：设置多久对日志文件名进行检查，看是否发生变化，默认是60s。
off：不使用缓存。默认为off
```

作用域：`http`、`server`、location

基本用法：

```shell
open_log_file_cache max=1000 inactive=20s valid=1m min_uses=2;
#例子中，设置缓存最多缓存1000个日志文件描述符，20s内如果缓存中的日志文件描述符至少被被访问2次，才不会被缓存关闭。每隔1分钟检查缓存中的文件描述符的文件名是否还存在。
```

### log_not_found指令 
这一指令的作用是配置是否将not found错误记录到error_log中。
作用域：http、server、location
语法： log_not_found on|off;
默认值on，即记录至error_log中。