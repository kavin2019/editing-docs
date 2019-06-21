## 理解dockerfile

### 为什么需要dockerfile

从 `docker commit` 的学习中，我们可以了解到，镜像的定制实际上就是定制每一层所添加的配置、文件。如果我们可以把每一层修改、安装、构建、操作的命令都写入一个脚本，用这个脚本来构建、定制镜像，那么之前提及的无法重复的问题、镜像构建透明性的问题、体积的问题就都会解决。这个脚本就是 Dockerfile。

Dockerfile 是一个文本文件，其内包含了一条条的 **指令(Instruction)**，每一条指令构建一层，因此每一条指令的内容，就是描述该层应当如何构建。

### 准备dockerfile

```shell
FROM nginx
RUN echo '<h1>Hello, Docker!</h1>' > /usr/share/nginx/html/index.html
```

### 构建镜像

在dockerfile文件所在的目录下执行：

```shell
$ docker build -t nginx:v3 .
Sending build context to Docker daemon 2.048 kB
Step 1 : FROM nginx
 ---> e43d811ce2f4
Step 2 : RUN echo '<h1>Hello, Docker!</h1>' > /usr/share/nginx/html/index.html
 ---> Running in 9cdc27646c7b
 ---> 44aa4490ce2c
Removing intermediate container 9cdc27646c7b
Successfully built 44aa4490ce2c
```

从输出中我们可以清楚的看到镜像的构建过程。在step2中，RUN指令启动了一个容器9cdc27646c7b，其镜像就是上一步step1生成的`e43d811ce2f4`,执行了RUN后的命令后，并提交了这一层44aa4490ce2c，随后删除了容器9cdc27646c7b。

这里我们用到了命令`docker build`

```shell
docker build [OPTIONS] <context>
OPTIONS:
-f, --file string  Dockerfile的文件名(默认为'PATH/Dockerfile')
-q, --quiet        静默模式，最后输出成功后的镜像ID
-t, --tag list     生成镜像的名称和tag。格式`'name:tag'`
```

这里我们通过`-t nginx:v3`指定镜像名和tag。

### 构建镜像的上下文

在上面例题中，我们使用`docker build -t nginx:v3 .`来构建镜像，命令最后的点`'.'`并不是表示当前dockerfile所在的目录（这个很容易被误解），对应命令格式，这个点表示的是context（上下文路径）。

> **什么是上下文**
>
> 首先我们要理解 `docker build` 的工作原理。Docker 在运行时分为 Docker 引擎（也就是服务端守护进程）和客户端工具。Docker 的引擎提供了一组 REST API，被称为 [Docker Remote API](https://docs.docker.com/develop/sdk/)，而如 `docker` 命令这样的客户端工具，则是通过这组 API 与 Docker 引擎交互，从而完成各种功能。因此，虽然表面上我们好像是在本机执行各种 `docker` 功能，但实际上，一切都是使用的远程调用形式在服务端（Docker 引擎）完成。也因为这种 C/S 设计（提供了丰富的API），让我们操作远程服务器的 Docker 引擎变得轻而易举。
>
> 当我们进行镜像构建的时候，并非所有定制都会通过 `RUN` 指令完成，经常会需要将一些本地文件复制进镜像，比如通过 `COPY` 指令、`ADD` 指令等。而 `docker build` 命令构建镜像，其实并非在本地构建，而是在服务端，也就是 Docker 引擎中构建的。**那么在这种客户端/服务端的架构中，如何才能让服务端获得本地文件呢？**
>
> 这就引入了上下文的概念。当构建的时候，用户会指定构建镜像上下文的路径，**`docker build` 命令得知这个路径后，会将路径下的所有内容打包，然后上传给 Docker 引擎。**这样 Docker 引擎收到这个上下文包后，展开就会获得构建镜像所需的一切文件。

例如：如果在dockerfile中出现：

```shell
COPY ./package.config /app/
```

这并不是要拷贝当前工作目录下的package，也不是dockerfile所在目录下的package.config。而是指定的上下文路径（context）下的package.config文件。

所以如果使用类似`COPY ../package.json /app` 或者 `COPY /opt/xxxx /app` 都无法工作的，因为它们超出了上下文目录的范围，docker引擎无法获取到这些路径下的文件。如果真的需要这些文件，需要将文件先复制到上下文目录中。

docker build命令会将上下文目录下的所有文件都打包发送到docker引擎，所以上下文目录中尽量只有所需要的文件（最好指定空目录，然后将所需的文件复制到目录中），不要指定包含太多无关的数据的目录。尤其别指定为根目录`/`。如此会将更个根目录（可能几十个G的数据）打包，然后发送。

dockerfile文件的路径是通过`-f`指定的，所以不一定需要命名为Dockerfile。