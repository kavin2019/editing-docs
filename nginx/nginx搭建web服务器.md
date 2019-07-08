## 搭建web服务器

### 简介

nginx可以用于搭建静态web服务器，而且效率相对于apache、tomcat、iis来说会高不少。

### 下载安装nginx（省略）

### 搭建简单的web服务器

创建web目录

```shell
$ mkdir /var/html
$ echo 'welcome!' > /var/html/index.html
```

配置文件：

```shell

worker_processes  1;

events {
    worker_connections  1024;
}

http {
    include       mime.types;
    default_type  application/octet-stream;
    access_log  logs/access.log ;
    error_log   logs/error.log;
    sendfile        on;
    keepalive_timeout  65;
    server {
        listen       80;
        server_name  localhost;
        location / {
            root   /var/html;
            index  index.html index.htm;
        }

        error_page   500 502 503 504  /50x.html;
        location = /50x.html {
            root   html;
        }
    }

}
```

检查并重启nginx:

```shell
$ nginx -t
$ nginx -s reload
```

检测：

```shell
$ curl localhost:80
welcome!

```





