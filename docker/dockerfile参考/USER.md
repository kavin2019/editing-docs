## USER 指定工作用户

格式：

```shell
USER <用户|用户ID>[:<用户组|用户组ID>]
```

USER指令和WORKDIR指令一样可以改变环境状态并影响以后的层，同时也会影响镜像运行后的容器环境。USER改变了后续治疗RUN、CMD以及ENTRYPOINT这类命令的使用的用户。

USER指令只是切换用户，该用户需要事先创建好的，否则无法切换。

```shell
RUN groupadd -r redis && useradd -r -g redis redis
USER redis
RUN [ "redis-server" ]
```

在`docker run`命令时使用`-u` 可以指定用户覆盖USER的设置。

### 拓展

如果以root用户执行命令，在命令执行期间希望改变身份，比如希望以某个已经建立好的用户执行某个服务，不要使用su和sudo，这些都需要麻烦的配置，而且在tty缺失的情况下会出错。建议使用sugo。

```Dockerfile
# 建立 redis 用户，并使用 gosu 换另一个用户执行命令
RUN groupadd -r redis && useradd -r -g redis redis
# 下载 gosu
RUN wget -O /usr/local/bin/gosu "https://github.com/tianon/gosu/releases/download/1.7/gosu-amd64" \
    && chmod +x /usr/local/bin/gosu \
    && gosu nobody true
# 设置 CMD，并以另外的用户执行
CMD [ "exec", "gosu", "redis", "redis-server" ]
```