## ENTRYPOINT 入口点

ENTRYPOINT指令的格式和CMD格式一样，分为shell格式和exec格式。

ENTRYPOINT的目的和CMD的目的是一样，都是为容器指定启动时的默认命令。只不过，如果指定了ENTRYPOINT指令，那么CMD指令的意义就不一样了，那么CMD不在是指定容器的默认命令，而是为ENTRYPOINT指令指定的命令提供参数。即实际执行是，变化成如下格式：

```shell
ENTRYPOINT "<CMD>"
```

下面两个实际例题用来理解下：

### 让镜像变得像命令一样使用

假设我们需要一个得知自己当前公网 IP 的镜像，那么可以先用 `CMD` 来实现：

```bash
FROM ubuntu:18.04
RUN apt-get update \
    && apt-get install -y curl \
    && rm -rf /var/lib/apt/lists/*
CMD [ "curl", "-s", "https://ip.cn" ]
```

假如我们使用 `docker build -t myip .` 来构建镜像的话，如果我们需要查询当前公网 IP，只需要执行：

```bash
$ docker run myip
当前 IP：61.148.226.66 来自：北京市 联通
```

这么看起来好像可以直接把镜像当做命令使用了，不过命令总有参数，如果我们希望加参数呢？比如从上面的 `CMD` 中可以看到实质的命令是 `curl`，那么如果我们希望显示 HTTP 头信息，就需要加上 `-i` 参数。那么我们可以直接加 `-i` 参数给 `docker run myip` 么？

```bash
$ docker run myip -i
docker: Error response from daemon: invalid header field value "oci runtime error: container_linux.go:247: starting container process caused \"exec: \\\"-i\\\": executable file not found in $PATH\"\n".
```

这是为什么呢？因为CMD指令时讲过，在`docker run`时指定命令会代替容器中的默认命令，也就是实际上`-i`替换了dockerfile中的`CMD [ "curl", "-s", "https://ip.cn" ]`命令，而不是在原来的CMD后加上参数`-i`。如果要实现，我们需要完整的指定命令

```bash
$ docker run myip curl -s https://ip.cn -i
```

这显然不是好的解决方案！此时ENTRYPOINT就可以出场了，我们使用ENTRYPOINT来解决这个问题：

```shell
FROM ubuntu:18.04
RUN apt-get update \
    && apt-get install -y curl \
    && rm -rf /var/lib/apt/lists/*
ENTRYPOINT [ "curl", "-s", "https://ip.cn" ]
```

这样子，我们就可以将镜像当作命令来使用了：

```shell
$ docker run myip -i
```

这里-i作为CMD传递给ENTRYPOINT作为参数，从而达到效果。

也可以通过`docker run --entrypoint`重写`ENTRYPOINT`入口点。

### 应用运行前的准备工作

启动容器就是启动主进程，但有些时候，启动主进程前，需要一些准备工作。

比如 `mysql` 类的数据库，可能需要一些数据库配置、初始化的工作，这些工作要在最终的 mysql 服务器运行之前解决。

此外，可能希望避免使用 `root` 用户去启动服务，从而提高安全性，而在启动服务前还需要以 `root` 身份执行一些必要的准备工作，最后切换到服务用户身份启动服务。或者除了服务外，其它命令依旧可以使用 `root` 身份执行，方便调试等。

这些准备工作是和容器 `CMD` 无关的，无论 `CMD` 为什么，都需要事先进行一个预处理的工作。这种情况下，可以写一个脚本，然后放入 `ENTRYPOINT` 中去执行，而这个脚本会将接到的参数（也就是 `<CMD>`）作为命令，在脚本最后执行。比如官方镜像 `redis` 中就是这么做的：

```dockerfile
FROM alpine:3.4
...
RUN addgroup -S redis && adduser -S -G redis redis
...
ENTRYPOINT ["docker-entrypoint.sh"]

EXPOSE 6379
CMD [ "redis-server" ]
```

可以看到其中为了 redis 服务创建了 redis 用户，并在最后指定了 `ENTRYPOINT` 为 `docker-entrypoint.sh` 脚本。

```bash
#!/bin/sh
...
# allow the container to be started with `--user`
if [ "$1" = 'redis-server' -a "$(id -u)" = '0' ]; then
    chown -R redis .
    exec su-exec redis "$0" "$@"
fi

exec "$@"
```

该脚本的内容就是根据 `CMD` 的内容来判断，如果是 `redis-server` 的话，则切换到 `redis` 用户身份启动服务器，否则依旧使用 `root` 身份执行。比如：

```bash
$ docker run -it redis id
uid=0(root) gid=0(root) groups=0(root)
```