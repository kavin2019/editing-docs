## LABEL添加元数据到镜像

### 格式

> LABEL <key>=<value> [<key>=<value>...]

LABEL指令是将元数据添加到镜像中。一个label是由键值对组成，要在label中包含空格，需要使用单引号或者双引号。就像在命令行中解析一样。例如：

```shell
LABEL "com.example.vendor"="ACME Incorporated"
LABEL com.example.label-with-value="foo"
LABEL version="1.0"
LABEL description="This text illustrates \
that label-values can span multiple lines." 
```

一个dockerfile可以多次调用LABEL，但是由于docker使用uniofs文件系统，每调用一次就会新增一个层，会导致镜像的冗余和臃肿，所以建议一个dockerfile中尽量使用一个LABEL指令。例如：

```shell
LABEL multi.label1="value1" multi.label2="value2" other="value3"
```

或

```shell
LABEL multi.label1="value1" \
			multi.label2="value2" \
			other="value3"
#这种写法等价于上方的实例，只是利用\换行后更利于阅读。
```

元数据是累积的，包括FROM指令指定的镜像，如果元数据中的key重复，那么后面的新值会覆盖前面的旧值。要查看镜像的LABEL，使用docker inpect。例如：

```shell
"Labels": {
                "maintainer": "NGINX Docker Maintainers <docker-maint@nginx.com>"
            },
```

