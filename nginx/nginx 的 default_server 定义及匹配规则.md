# nginx 的 default_server 定义及匹配规则

nginx 的 default_server 指令可以定义默认的 server 去处理一些没有匹配到 server_name 的请求，如果没有显式定义，则会选取第一个定义的 server 作为 default_server。

在了解到如上规则后，我们可以捕获未做绑定的域名访问或直接IP访问，做重定向到 403 页面等处理。

nginx 隐式的 default server
```
http {
    # 如果没有显式声明 default server 则第一个 server 会被隐式的设为 default server
    server {
        listen 80;
        server_name _; # _ 并不是重点 __ 也可以 ___也可以
        return 403; # 403 forbidden
    }
    
    server {
        listen 80;
        server_name www.a.com;
        ...
    }
    
    server {
        listen 80;
        server_name www.b.com;
        ...
    }
}
```
很多人复制粘贴广泛传播 server_name 要设为 `'_'`，其实一毛钱的关系也没有。`'_'` 只是作为一个和业务域名无关的请求回收服务而已，如果我们线上的业务都是明确的业务域名访问，那泛解析造成的一些非业务域名或ip访问都会被这个 server 回收处理。

在没有显式定义 default server 时，nginx 会将配置的第一个 server 作为 default server，即当请求没有匹配任何 server_name 时，此 server 会处理此请求。所以，当我们直接使用 ip 访问时会被交给此处定义的第一个 server 处理，403 forbidden。

显示的定义一个 default server
```
http {
    server {
        listen 80;
        server_name www.a.com;
        ...
    }
    
    server {
        listen 80;
        server_name www.b.com;
        ...
    }
    
    # 显示的定义一个 default server
    server {
        listen 80 default_server;
        server_name _;
        return 403; # 403 forbidden
    }
    
}
```
建议显示指定 default server，因为我们在配置虚拟主机或多业务时，会存有多个 server 配置文件，如果使用隐式方式选取第一个被载入的 server 作为 default server 的话，我们还要时刻去确认谁是被第一个载入的...制造风险...

直接指定server_name 为 ip（只能禁止ip访问）
```
http {
    server {
        listen 80;
        server_name www.a.com;
        ...
    }
    
    server {
        listen 80;
        server_name www.b.com;
        ...
    }
    
    # 直接指定 ip server_name
    server {
        listen 80;
        server_name xxx.xxx.xxx.xxx;
        return 403; # 403 forbidden
    }
    
}
```
以上三种方式都可禁止 ip 直接访问，且 1,2 同时可以禁止未绑定域名的访问（比如你泛解析了主域名）。

小记：nginx 批量载入配置 conf 时会按 ascii 排序载入，这就会以 server_a.conf server_b.conf server_c.conf 的顺序载入，如果没有生命 default_server 的话，那 server_a 会作为默认的 server 去处理 未绑定域名/ip 的请求。
