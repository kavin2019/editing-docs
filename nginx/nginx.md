# nginx
***
## 简介
nginx是一个轻量级的负载均衡器，可以作为静态资源的服务器，也可以作为负载均衡进行消息转发。它可以配合keepalived实现高可用。

nginx在流行互联网中所占位置：
![](../images/population-frame.jpg)

## 代理

### 正向代理

代理，就是当一个对象不能或者无法直接完成某种行为，由第三方对象来实现。

正向代理在互联网中，就是代理服务器代替客户端去想web服务器发起请求。如下图：国内由于防火墙的原因无法访问youtube，那么可以通过第三方的vpn作为代理访问，那么vpn就是正向代理，它代理的是客户端。而且客户端是不知道vpn的存在，只知道目标web服务器的存在

![](/Users/james/Documents/docs/Markdown/images/postive-proxy.jpg)

### 反向代理

有正向代理，那么也就有反向代理，反向代理代理的是后台web服务器，客户端访问代理服务器，然后由代理服务器将请求分发给后续的web服务器。

![](../images/reverse-proxy.jpg)

### Master和worker工作模式

```
[root@localhost apk]# ps aux | grep  nginx
root      5546  0.0  0.0  21312  1548 ?        Ss   May26   0:00 nginx: master process /usr/local/nginx/sbin/nginx -c /usr/local/nginx/conf/nginx.conf
nginx    29222  0.0  0.0  21312  1704 ?        S    16:23   0:00 nginx: worker process
```

nginx工作模式如下：

 ```  
        | -> worker -> request
master —| -> worker -> request
        ......中间多个worker
        | -> worker -> request
 ```
 master:
 	* 读取config
 	* 验证config
 	* 管理worker
 worker:
  * 处理连接和请求。
  * worker的个数是由配置文件决定的。配置节点：`worker_processes: 1`。一般数量由cpu个数决定


## 安装

## 配置

nginx的主配置文件：nginx.config。

### 全局配置

* worker-processes
  nginx工作室worker进程数。一般对应cpu数量。查看cpu数量：``
* 
* `worker_cpu_affinity`
  在高并发情况下，通过设置cpu粘性来降低由于多CPU核切换造成的寄存器等现场重建带来的性能损耗。如`worker_cpu_affinity 0001 0010 0100 1000;` （四核）
* `worker_connections 2048`
  写在*events*部分。每一个worker进程能并发处理（发起）的最大连接数（包含与客户端或后端被代理服务器间等所有连接数）。nginx作为反向代理服务器，计算公式 `最大连接数 = worker_processes * worker_connections/4`，所以这里客户端最大连接数是1024，这个可以增到到8192都没关系，看情况而定，但不能超过后面的`worker_rlimit_nofile`。当nginx作为http服务器时，计算公式里面是除以2
* `worker_rlimit_nofile 10240`
  写在*main*部分。默认是没有设置，可以限制为操作系统最大的限制65535
* `use epoll`
  写在`events`部分。在Linux操作系统下，nginx默认使用epoll事件模型，得益于此，nginx在Linux操作系统下效率相当高。同时Nginx在OpenBSD或FreeBSD操作系统上采用类似于epoll的高效事件模型kqueue。在操作系统不支持这些高效模型时才使用select

### HTTP服务器

提供http服务相关的一些配置参数

* sendfile on
开启高效文件传输模式，sendfile指令指定nginx是否调用sendfile函数来输出文件，减少用户空间到内核空间的上下文切换。对于普通应用设为 on，如果用来进行下载等应用磁盘IO重负载应用，可设置为off，以平衡磁盘与网络I/O处理速度，降低系统的负载
  
* keepalive_timeout 65
长连接超时时间，单位是秒。长连接请求大量小文件的时候，可以减少重建连接的开销，但假如有大文件上传，65s内没上传完成会导致失败。如果设置时间过长，用户又多，长时间保持连接会占用大量资源
  
* send_timeout
用于指定响应客户端的超时时间。这个超时仅限于两个连接活动之间的时间，如果超过这个时间，客户端没有任何活动，Nginx将会关闭连接
  
* client_max_body_size 10m
允许客户端请求的最大单文件字节数。如果有上传较大文件，请设置它的限制值
  
* client_body_buffer_size 128k
缓冲区代理缓冲用户端请求的最大字节数
  
  
  
### http_proxy模块
nginx作为反向代理服务器的功能，包括缓存功能时的配置
* proxy_connect_timeout 60
	nginx跟后端服务器连接超时时间(代理连接超时)
	
* proxy_read_timeout 60
	连接成功后，与后端服务器两个成功的响应操作之间超时时间(代理接收超时)
	
* proxy_buffer_size 4k
	proxy_buffers缓冲区，nginx针对单个连接缓存来自后端realserver的响应，网页平均在32k以下的话，这样设置
  
* proxy_busy_buffers_size 64k
	高负荷下缓冲大小（proxy_buffers*2）
  
* proxy_max_temp_file_size
	当 proxy_buffers 放不下后端服务器的响应内容时，会将一部分保存到硬盘的临时文件中，这个值用来设置最大临时文件大小，默认1024M，它与 proxy_cache 没有关系。大于这个值，将从upstream服务器传回。设置为0禁用
  
* proxy_temp_file_write_size 64k
	当缓存被代理的服务器响应到临时文件时，这个选项限制每次写临时文件的大小。`proxy_temp_path`（可以在编译的时候）指定写到哪那个目录。
  
* proxy_pass
	
* proxy_redirect
	
### http_zip模块

* gzip on
  开启gzip压缩输出，减少网络传输
* gzip_min_length 1k
  设置允许压缩的页面最小字节数，页面字节数从header头得content-length中进行获取。默认值是20。建议设置成大于1k的字节数，小于1k可能会越压越大
* gzip_buffers 4 16k
  设置系统获取几个单位的缓存用于存储gzip的压缩结果数据流。4 16k代表以16k为单位，安装原始数据大小以16k为单位的4倍申请内存。
* gzip_http_version 1.0
  用于识别 http 协议的版本，早期的浏览器不支持 Gzip 压缩，用户就会看到乱码，所以为了支持前期版本加上了这个选项，如果你用了 Nginx 的反向代理并期望也启用 Gzip 压缩的话，由于末端通信是 http/1.0，故请设置为 1.0
* gzip_comp_level 6
  gzip压缩比（1-9），1压缩比最小处理速度最快，9压缩比最大但处理速度最慢(传输快但比较消耗cpu)
* gzip_types
  匹配mime类型进行压缩，无论是否指定,”text/html”类型总是会被压缩的
* gzip_proxied any
   Nginx作为反向代理的时候启用，决定开启或者关闭后端服务器返回的结果是否压缩，匹配的前提是后端服务器必须要返回包含”Via”的 header头。
* gzip_vary on
  和http头有关系，会在响应头加个 Vary: Accept-Encoding ，可以让前端的缓存服务器缓存经过gzip压缩的页面，例如，用Squid缓存经过Nginx压缩的数据

### 虚拟主机Server模块

http服务上支持若干虚拟主机。每个虚拟主机一个对应的server配置项，配置项里面包含该虚拟主机相关的配置。在提供mail服务的代理时，也可以建立若干server。每个server通过监听地址或端口来区分

* listen
  监听端口，默认80，小于1024的要以root启动。可以为`listen *:80`、`listen 127.0.0.1:80`等形式

* server_name
  服务器名或IP地址，如`localhost`、`www.example.com`，可以通过正则匹配

* location [pattern]
  http服务中，某些特定的URL对应的一系列配置项。配置项会应用到url匹配pettern的。

  * root /var/www/html
    静态资源路由。定位到服务器上的静态资源上
    
  * index index.jsp index.html index.htm
      定义路径下默认访问的文件名，一般跟着`root`放
    
  * proxy_pass `http://backend`
    请求转向backend定义的服务器列表，即反向代理，对应`upstream`负载均衡器。也可以通过指定域名或ip和端口来转发到其它服务器
    例如：`proxy_pass http://ip:port` 或`proxy_pass http://域名`或`proxy_pass http://backend`
  
    ```
    统一访问 localhost/proxy/test 时：
    第一种：
    location /proxy/ {
    		//url后带有/，所以不会带匹配到路径部分
        proxy_pass http://127.0.0.1/;
    }
    代理到URL：http://127.0.0.1/test.html
    
    第二种（相对于第一种，最后少一个 / ）
    location /proxy/ {
    		//url后没有/，所以会带上匹配部分
        proxy_pass http://127.0.0.1;
    }
    代理到URL：http://127.0.0.1/proxy/test.html
    
    
    第三种：
    location /proxy/ {
        proxy_pass http://127.0.0.1/aaa/;
    }
    代理到URL：http://127.0.0.1/aaa/test.html
    
    
    第四种（相对于第三种，最后少一个 / ）
    location /proxy/ {
        proxy_pass http://127.0.0.1/aaa;
    }
    代理到URL：http://127.0.0.1/aaatest.html
    ```
  
    
  
  * `proxy_redirect off;`
  
  * `proxy_set_header Host $host;`
     转发请求时，默认后段的服务器认为请求是方向代理服务器发起的，可以通过这个选项以及下面的请求设置转发的请求头信息。设置转发头的主机名
  
  * `proxy_set_header X-Real-IP $remote_addr;`
     设置转发头的IP地址。如果不设置默认是反向代理服务器的地址
  
  * proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for
     设置代理头
  
  #### location 匹配规则
  * 以`=`开头表示精确匹配
    只匹配根目录结尾的请求，后面不能带任何字符串。
  
  * `^~` 开头表示uri以某个常规字符串开头，不是正则匹配
  
  * ~ 开头表示区分大小写的正则匹配;
  
  * ~* 开头表示不区分大小写的正则匹配
  
  * / 通用匹配, 如果没有其它匹配,任何请求都会匹配到
  
    顺序 no优先级：
     (location =) > (location 完整路径) > (location ^~ 路径) > (location ~,~* 正则顺序) > (location 部分起始路径) > (/)

### upstream模块

upstream可以设置一组后端服务器，实现负载均衡的核心模块。


  * upstream配置负载均衡服务器。
  ```
  upstream bech
  {
    server 127.0.0.1
  }
  ```

* server下的配置
  * down 表示当前的server暂时不参与负载
  * weight 默认为1.weight越大，负载的权重就越大。
  * max_fails ：允许请求失败的次数默认为1.当超过最大次数时，返回proxy_next_upstream 模块定义的错误
  * fail_timeout: max_fails次失败后，暂停的时间。
  * backup： 其它所有的非backup机器down或者忙的时候，请求backup机器。所以这台机器压力会最轻

### 其它模块

访问控制，请求会依次匹配，如果满足了任何一个就不再执行后续的所有访问控制。直接执行或拒绝访问

* allow
  访问控制，允许IP或ip段地址访问，可以构建白名单。例如：

  ```
  allow 192.168.254.01
  allow 192.168.254.02
  allow 192.168.254.03/24 //ip段
  ```

  

* deny
  访问控制，拒绝IP或ip段访问，可以构建黑名单。例如：

  ```
  deny 192.168.254.01
  deny all
  ```

  

* autoindex on
  Nginx默认是不允许列出整个目录的。

* autoindex_exact_size off
  默认为on，显示出文件的确切大小，单位是bytes。改为off后，显示出文件的大概大小，单位是kB或者MB或者GB

* autoindex_localtime on
  默认为off，显示的文件时间为GMT时间。改为on后，显示的文件时间为文件的服务器时间

## nginx启动

```
启动：nginx start
重启：nginx reload     
```
## nginx停止
```
nginx -s stop
或 pkill nginx
```
## nginx检查配置
```
nginx -t 
```
## nginx热启动
当配置文件修改后是不会自动应用到已经启动的nginx中的。通过热启动就可在不停止服务（不用nginx stop）的情况下应用配置。命令：
```
nignx -s reload
```
热启动方法：
  master使用新的配置文件启动初始化新的worker进程，然后将请求分配到新的worker进程。最后原来的worker进程在处理完请求后被kill掉。

## 通用

```shell
user  www www;
worker_processes  2;

error_log  logs/error.log;
#error_log  logs/error.log  notice;
#error_log  logs/error.log  info;

pid        logs/nginx.pid;


events {
    use epoll;
    worker_connections  2048;
}


http {
    include       mime.types;
    default_type  application/octet-stream;

    #log_format  main  '$remote_addr - $remote_user [$time_local] "$request" '
    #                  '$status $body_bytes_sent "$http_referer" '
    #                  '"$http_user_agent" "$http_x_forwarded_for"';

    #access_log  logs/access.log  main;

    sendfile        on;
    # tcp_nopush     on;

    keepalive_timeout  65;

  # gzip压缩功能设置
    gzip on;
    gzip_min_length 1k;
    gzip_buffers    4 16k;
    gzip_http_version 1.0;
    gzip_comp_level 6;
    gzip_types text/html text/plain text/css text/javascript application/json application/javascript application/x-javascript application/xml;
    gzip_vary on;
  
  # http_proxy 设置
    client_max_body_size   10m;
    client_body_buffer_size   128k;
    proxy_connect_timeout   75;
    proxy_send_timeout   75;
    proxy_read_timeout   75;
    proxy_buffer_size   4k;
    proxy_buffers   4 32k;
    proxy_busy_buffers_size   64k;
    proxy_temp_file_write_size  64k;
    proxy_temp_path   /usr/local/nginx/proxy_temp 1 2;

  # 设定负载均衡后台服务器列表 
    upstream  backend  { 
              #ip_hash; 
              server   192.168.10.100:8080 max_fails=2 fail_timeout=30s ;  
              server   192.168.10.101:8080 max_fails=2 fail_timeout=30s ;  
    }

  # 很重要的虚拟主机配置
    server {
        listen       80;
        server_name  itoatest.example.com;
        root   /apps/oaapp;

        charset utf-8;
        access_log  logs/host.access.log  main;

        #对 / 所有做负载均衡+反向代理
        location / {
            root   /apps/oaapp;
            index  index.jsp index.html index.htm;

            proxy_pass        http://backend;  
            proxy_redirect off;
            # 后端的Web服务器可以通过X-Forwarded-For获取用户真实IP
            proxy_set_header  Host  $host;
            proxy_set_header  X-Real-IP  $remote_addr;  
            proxy_set_header  X-Forwarded-For  $proxy_add_x_forwarded_for;
            proxy_next_upstream error timeout invalid_header http_500 http_502 http_503 http_504;
            
        }

        #静态文件，nginx自己处理，不去backend请求tomcat
        location  ~* /download/ {  
            root /apps/oa/fs;  
            
        }
        location ~ .*\.(gif|jpg|jpeg|bmp|png|ico|txt|js|css)$   
        {   
            root /apps/oaapp;   
            expires      7d; 
        }
       	location /nginx_status {
            stub_status on;
            access_log off;
            allow 192.168.10.0/24;
            deny all;
        }

        location ~ ^/(WEB-INF)/ {   
            deny all;   
        }
        #error_page  404              /404.html;

        # redirect server error pages to the static page /50x.html
        #
        error_page   500 502 503 504  /50x.html;
        location = /50x.html {
            root   html;
        }
    }

  ## 其它虚拟主机，server 指令开始
}
```



## 参考文章

* [nginx配置location和rewrite总结]([http://seanlook.com/2015/05/17/nginx-location-rewrite/](http://seanlook.com/2015/05/17/nginx-location-rewrite/))
* [nginx服务器安装及配置文件详解]([http://seanlook.com/2015/05/17/nginx-install-and-config/](http://seanlook.com/2015/05/17/nginx-install-and-config/))