# nginx服务器安装及配置文件详解

***

## 1. 安装

### 选择版本

从[下载地址](http://nginx.org/en/download.html)下载，建议下载稳定（stable）版本。下载后解压到/usr/local/src中

### 安装条件

nginx安装前需要先安装一下两个模块：

* GCC
* PCRE

### 额外模块

添加一些辅助模块

* [nginx_upstream_check_module-0.3.0.tar.gz](https://github.com/yaoweibin/nginx_upstream_check_module/releases)   检查后端服务器的状态
* [nginx-goodies-nginx-sticky-module-ng-bd312d586752.tar.gz](https://bitbucket.org/nginx-goodies/nginx-sticky-module-ng/downloads) 后端做负载均衡解决session sticky问题（与upstream_check模块结合使用需要另外打补丁，请参考[nginx负载均衡配置实战](http://seanlook.com/2015/06/02/nginx-cache-check/)）

PS：注意插件与nginx的版本兼容问题

### 执行编译和安装

```shell
[root@cachets nginx-1.6.3]# pwd
/usr/local/src/nginx-1.6.3
[root@cachets nginx-1.6.3]# ./configure --prefix=/usr/local/nginx-1.6 --with-pcre \
> --with-http_stub_status_module --with-http_ssl_module \
> --with-http_gzip_static_module --with-http_realip_module \
> --add-module=../nginx_upstream_check_module-0.3.0

[root@cachets nginx-1.6.3]# make && make install
```



### configure 命令配置详解

nginx大部分常用模块，编译时`./configure --help`以`--without`开头的都默认安装。

- `--prefix=PATH` ： 指定nginx的安装目录。默认 `/usr/local/nginx`
- `--conf-path=PATH` ： 设置nginx.conf配置文件的路径。nginx允许使用不同的配置文件启动，通过命令行中的-c选项。默认为*prefix/conf/nginx.conf*
- `--user=name`： 设置nginx工作进程的用户。安装完成后，可以随时在nginx.conf配置文件更改user指令。默认的用户名是nobody。`--group=name`类似
- `--with-pcre` ： 设置PCRE库的源码路径，如果已通过yum方式安装，使用`--with-pcre`自动找到库文件。使用`--with-pcre=PATH`时，需要从PCRE网站下载pcre库的源码（版本4.4 - 8.30）并解压，剩下的就交给Nginx的`./configure`和`make`来完成。perl正则表达式使用在`location`指令和 `ngx_http_rewrite_module`模块中。
- `--with-zlib=PATH` ： 指定 zlib（版本1.1.3 - 1.2.5）的源码解压目录。在默认就启用的网络传输压缩模块`ngx_http_gzip_module`时需要使用zlib 。
- `--with-http_ssl_module` ： 使用https协议模块。默认情况下，该模块没有被构建。前提是openssl与openssl-devel已安装
- `--with-http_stub_status_module` ： 用来监控 Nginx 的当前状态
- `--with-http_realip_module` ： 通过这个模块允许我们改变客户端请求头中客户端IP地址值(例如X-Real-IP 或 X-Forwarded-For)，意义在于能够使得后台服务器记录原始客户端的IP地址
- `--add-module=PATH` ： 添加第三方外部模块，如nginx-sticky-module-ng或缓存模块。每次添加新的模块都要重新编译（Tengine可以在新加入module时无需重新编译）

### 推荐配置

```shell
./configure \
> --prefix=/usr \
> --sbin-path=/usr/sbin/nginx \
> --conf-path=/etc/nginx/nginx.conf \
> --error-log-path=/var/log/nginx/error.log \
> --http-log-path=/var/log/nginx/access.log \
> --pid-path=/var/run/nginx/nginx.pid  \
> --lock-path=/var/lock/nginx.lock \   
> --user=nginx \
> --group=nginx \
> --with-http_ssl_module \
> --with-http_stub_status_module \
> --with-http_gzip_static_module \
> --http-client-body-temp-path=/var/tmp/nginx/client/ \
> --http-proxy-temp-path=/var/tmp/nginx/proxy/ \
> --http-fastcgi-temp-path=/var/tmp/nginx/fcgi/ \
> --http-uwsgi-temp-path=/var/tmp/nginx/uwsgi \
> --with-pcre=../pcre-7.8
> --with-zlib=../zlib-1.2.3
```

### 拓展

* 查看安装配置

  > nginx -V

* 

