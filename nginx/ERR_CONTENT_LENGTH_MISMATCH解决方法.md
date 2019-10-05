## ERR_CONTENT_LENGTH_MISMATCH解决方法

### 问题描述

前端页面加载css，和js文件的时候，经常出现`ERR_CONTENT_LENGTH_MISMATCH`的报错情况。

### 定位问题

在单独打开`hearder`中css，js的网络地址是能打开的，所以排除了最简单的地址错误。前端项目是由nginx代理的，所以可以查看nginx的日志，看看有无线索。

进入`${NGINX_HOME}\logs`，查看`error.log`，得到如下信息：

```shell
2018/07/13 14:22:49 [crit] 275197#0: *1543 open() "/usr/local/nginx/proxy_temp/4/30/0000000304" failed (13: Permission denied) while reading upstream, client: 192.168.75.11, server: xxxx.xxxx.com, request: "GET /model-web/static/js/vendor.7e49e6e8578e1e242c55.js.map HTTP/1.1", upstream: "http://xxx.xxx.xxx:8080/model-web/static/js/vendor.7e49e6e8578e1e242c55.js", host: "xxx.xxx.xxx.xxx:8081"
```

线索很明显，在请求`vendor.7e49e6e8578e1e242c55.js`的时候，nginx在尝试访问`/usr/local/nginx/proxy_temp/4/30/0000000304`，结果因为没有权限，导致了请求失败。

那么，为什么nginx要访问`proxy_temp`文件夹呢，因为`proxy_temp`是nginx的缓存文件夹，我的css和js文件过大了，所以nginx一般会从缓存里面去拿，而不是每次都去原地址直接加载。

### 分析原因

1. 进入`/usr/local/nginx/proxy_temp`，查看文件权限。拥有者为root
2. 查看现在nginx的使用者，发现是nginx
3. 那么，导致没有权限的原因也查清了，就是文件的所有者和访问者不是同一用户

### 解决办法

1. 改变文件夹所有者，把文件夹及文件夹下所有文件的所有者改为当前nginx的使用者，`chown -R root:root ./*`。
2. 增加权限，给其他用户增加可读权限。
3. 修改nginx配置文件，声明使用者(推荐)。
4. 重启nginx，`./nginx -s reload`，问题解决