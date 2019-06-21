## 定制镜像

### 1. FROM指定基础镜像

所谓定制镜像，就是在原有的镜像的基础上进行新增、修改和删除后得到的新镜像。而原来的镜像就成为基础镜像，FROM指令就是用于指定新镜像的基础镜像，所以必须要有，而且必须在DOCKERFILE文件中的第一行。

#### 格式

```shell
FROM <image>[:tag]
或
FROM <image>:<digest>
```

在docker hub上提供了非常多高质量的镜像，可以用来作为基础镜像。docker hub中有一个特殊的镜像，名为scratch。这个镜像是虚拟的概念，并不实际存在，它表示一个空白镜像。不以任何系统做基础的镜像并不少见，比如 swarm、cores/etcd。对于 Linux 下静态编译的程序来说，并不需要有操作系统提供运行时支持，所需的一切库都已经在可执行文件里了，因此直接 `FROM scratch` 会让镜像体积更加小巧。使用 [Go 语言](https://golang.org/) 开发的应用很多会使用这种方式来制作镜像，这也是为什么有人认为 Go 是特别适合容器微服务架构的语言的原因之一。 

FROM有一下限制：

* FROM必须是Dockerfile文件中的第一个非注释命令
* 在一个Dockerfile中可以有多个FROM，用于创建多个镜像。只需要在新的FROM指令出现前，记录上次提交的镜像ID
* tag和digest可选，如果指定，默认是latest



### 2. RUN执行命令

RUN指令是用来执行命令行命令的。由于命令行功能强大，所以RUN指令在定制镜像时十分常用。其格式有两种：

* shell格式：`RUN <命令>`, 就像直接在命令行中执行命令一样。例如：

  ```shell
  RUN echo 'hello dokcer!' > a.txt
  ```

  

* exec格式：`RUN ["可执行文件","参数1","参数2",…]`,这种类似编程语言中的函数调用。例如：

  ```shell
  RUN ['echo', 'hell docker!']
  ```

问题：既然shell格式RUN指令和命令行一样那么在dockerfile文件是否也能单个单个命令执行呢？例如：

```shell
FROM debian:stretch

RUN apt-get update
RUN apt-get install -y gcc libc6-dev make wget
RUN wget -O redis.tar.gz "http://download.redis.io/releases/redis-5.0.3.tar.gz"
RUN mkdir -p /usr/src/redis
RUN tar -xzf redis.tar.gz -C /usr/src/redis --strip-components=1
RUN make -C /usr/src/redis
RUN make -C /usr/src/redis install
```

由于docker使用unionfs文件系统，所以dockerfile文件中的每一个指令都会新建一层，RUN也不例外，所以上面的写法RUN会新建7层，这完全没有意义的，而且在下载、安装、解压缩、编译过程中很多不需要的东西如编译环境、安装包等也会被装入到镜像中。结果会造成镜像非常的臃肿、非常多的层。不仅增加了构建部署的时间，而且容易出错。

Union FS是有最大层数限制的，比如：AUFS，曾经最大层数不超过42层，现在不超过127层

所以可以优化如下：

```shell
FROM debian:stretch

RUN buildDeps='gcc libc6-dev make wget' \
    && apt-get update \
    && apt-get install -y $buildDeps \
    && wget -O redis.tar.gz "http://download.redis.io/releases/redis-5.0.3.tar.gz" \
    && mkdir -p /usr/src/redis \
    && tar -xzf redis.tar.gz -C /usr/src/redis --strip-components=1 \
    && make -C /usr/src/redis \
    && make -C /usr/src/redis install \
    && rm -rf /var/lib/apt/lists/* \
    && rm redis.tar.gz \
    && rm -r /usr/src/redis \
    && apt-get purge -y --auto-remove $buildDeps
```

这里使用`&&`(也可以组合`;`)将所有的命令连接在一起，将7层降到了1层。并且使用换行符对命令进行了格式化。Dockerfile文件是支持`\`换行和`#`行首注释的，良好的格式，比如换行、缩进、注释等，会让维护、排障更为容易，这是一个比较好的习惯。所以在撰写dockerfile文件时，需要时刻提醒自己，这不是在写脚本，而是定义每一层如何构建。

此外，还可以看到这一组命令的最后添加了清理工作的命令，删除了为了编译构建所需要的软件，清理了所有下载、展开的文件，并且还清理了 `apt` 缓存文件。这是很重要的一步，我们之前说过，镜像是多层存储，每一层的东西并不会在下一层被删除，会一直跟随着镜像。因此镜像构建时，一定要确保每一层只添加真正需要添加的东西，任何无关的东西都应该清理掉。

很多人初学 Docker 制作出了很臃肿的镜像的原因之一，就是忘记了每一层构建的最后一定要清理掉无关文件。