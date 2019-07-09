## rewrite模块

### 简介

nginx的rewrite模块（ngx_http_rewrite_module）用于通过PCRE的正则表达式，return指令以及条件选择等配置来重写请求的URI。

rewrite模块包括以下指令（最后两个不常用）：

* break
* if
* return
* set
* rewrite
* rewrite_log
* uninitialized_variable_warn
* Internal Implementation

其中break，if，return，rewrite 和set指令处理顺序如下：

* 这些rewrite模块下的指令在`server`层级配置中顺序执行
* 重复执行：
  1.  匹配location
  2. 执行location中的rewrite模块指令，顺序执行
  3. 如果执行了rewrite，那么重新执行1步骤，循环次数不能超过10次

### 模块指令

#### break

用于停止rewrite模块指令的执行。但是其它模块的指令不受影响。

作用域：server，location， if

例如：

```shell
server {
        listen       80;
        server_name  localhost;
        charset utf-8;
        break; # 如果没有break，那么就会返回 this break test！
        return 200 'this break test!';
        location /breaktest {
        	  break;
        	  return 200 'this break test in location'
        	  proxy_pass http://localhost:80/other
        }
        location / {
            return 200 $request_uri;
        }
    }
    
```

输入测试地址：`http://localhost:80/breaktest`,请求到达server层级后，遇到break，停止rewrite模块的指令，执行location匹配，匹配到/breaktest{},有遇到break指令，停止return指令的执行，继续执行proxy_pass指令，反向代理执行后匹配location /{}，返回 200 /other



#### set

用于在server,location和if层级中设置变量

例如：

```shell

server {
        listen       80;
        server_name  localhost;
        location / {
            #set赋值，可以直接赋字符串，或是变量，如下是变量和字符串的组合
            set $set_value_test "your visited: $request_uri";
            return 200 $set_value_test;
        }
}
```

输入测试地址：http://localhost:80/other, 返回 http 200  your visited:/other



#### if

条件判断，用于决定时候执行if层级中的指令。

作用域：server location

判断条件：

* 变量值是否为空或0
  例如：

  ```shell
  set $if_value_test “0”;
    #注意这里if、()和{}要空格，否则会报错..略坑，不过这也是代码规范了
    if ($if_value_test) {
    #不会执行，因为$if_value_test值为0，false
    return 200;
  }
  ```

  

* 变量和字符串比较。`=`为等于，`!=`不等于

* 变量和正则表达式比较：`~为区分大小写匹配，~*为不区分大小写匹配，!~,!~*前面两者的非操作`
  例如：

  ```shell
  if ($request_uri ~ “^/breaktest$”) {
        #测试链接http://192.168.88.38/breaktest，最终返回http200
        return 200;
  }
  ```

* 检查文件是否存在，-f存在即为true，!-f不存在即为true

* 检查目录是否存在，-d存在即为true，!-d不存在即为true

* 检查文件或目录是否存在，-e存在即为true，!-e不存在即为true

* 检查文件是否可执行，-x可执行即为true，!-x不可执行即为true

  ```shell
  #检测配置文件是否存在
  if (-e /usr/local/nginx/conf) {
        return 200;
  }
  #请求是否来自IE浏览器，如果是重写URL
  if ($http_user_agent ~ MSIE) {
      rewrite ^(.*)$ /msie/$1 break;
  }
  #利用正则表达式，不区分大小写判断cookie中是否存储了id值 
  if ($http_cookie ~* "id=([^;]+)(?:;|$)") {
  	  #如果存储了，则将值设置给变量$id
      set $id $1;
  }
  #字符串条件判断
  if ($request_method = POST) {
      return 405;
  }
  #值条件判断
  if ($slow) {
      limit_rate 10k;
  }
  #值条件判断
  if ($invalid_referer) {
      return 403;
  }
  ```



#### return

格式：

```shell
return code [text] #返回状态码，以及响应体中的文本内容
return code URL #返回响应码（一般是301, 302, 303, 307, 以及 308），以及重定向的URL
return URL #重定向到本server上
```



#### rewrite

格式：

```shell
rewrite regex replacement [flag]
#用正则表达式regex匹配URL中特定部分，然后用replacement替换，即完成重写。
regex：正则表达式
replacement：重写的替换内容，可以使用正则表达式匹配的部分。如果replacement以http:// https://或$schema开头那么处理停止，重定向到replacement对应的web服务器，然后返回结果。
flag：标识位，个人理解为重写url后的行为。flag值：break，last，redirect，permanent
      break：和上面讲的break用途及用法一样，终止执行rewrite模块指令集；
      last：终止执行rewrite模块指令集，并开始搜寻重写url后匹配的location；
      redirect：临时重定向
      permanent：永久重定向
```

作用域：server， location， if

例如：

replacement带http://,https://或$schema

```shell
location / {
    #测试链接http://192.168.88.38/test/，被重定向到百度首页，后面的语句不会再执行
    rewrite /test/(.*) http://www.baidu.com;
    return 200 '不会执行到这里';
}
```

replacement不带http:

```shell
location / {
  #测试链接http://192.168.88.38/test/，匹配到location /{}后url被重写http://192.168.88.38/breaktest，继续搜索匹配
  #匹配到location = /breaktest{}，最终返回http200及this is breaktest
  rewrite /test/(.*) /breaktest;
}
location = /breaktest {
  return 200 "this is breaktest";
}

```

rewrite不带flag时，多个rewrite指令顺序执行，档location中没有可执行的rewrite指令时，重新发起location的匹配。例如：

```shell
 location / {
    #测试链接http://192.168.88.38/test1，匹配到location / {}
    rewrite ^/test1 /test2;#被重写为/test2，继续往下执行rewrite
    rewrite ^/test2 /test3;#被重写为/test3，往下没有可执行的rewrite模块指令，发起一次location匹配，匹配到		location /test3 {}，最终返回http200及/test3
 }
location /test2 {
 		return 200 "/test2";
 }
 location /test3 {
 			return 200 "/test3";
 }
```

ast和break的区别在于，last会发起新的location匹配，而break不会。例如：

```shell

location / {
  rewrite ^/test1 /test2;
  rewrite ^/test2 /test3 last;
  rewrite ^/test3 /test4;
}
location /test2 {
  return 200 "/test2";
}
location /test3 {
  return 200 "/test3";
}
location /test4 {
  return 200 "/test4";
}
```

输入测试地址：`http://localhost:80/test1`后,请求先匹配到location / {},然后请求地址被重写为/test2,继续执行第二个rewrite被重写为/test3,因为flag为last，那么不继续向下执行重写，而是重新发起location匹配，匹配到/test3{},然后返回http 200 /test3。

如果把第二个rewrite的flag改为break，那么重写为/test3后，不在继续执行，也不发起location匹配，没有匹配资源，返回404。