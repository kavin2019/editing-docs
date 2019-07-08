## MAINTAINER

使用MAINTAINER指令来为生成的镜像署名作者和联系方式

```shell
MAINTAINER 'kavin' 'kavin@gmail.com'
```



##  STOPSIGNAL

`STOPSIGNAL`用于设置停止容器所要发送的系统调用信号：

```
STOPSIGNAL signal
```

所使用的信号必须是内核系统调用表中的合法的值，如：`9`、`SIGKILL`。



## SHELL

`SHELL`用于设置执行命令（`shell`式）所使用的的默认`shell`类型：

```
SHELL ["executable", "parameters"]
```

`SHELL`在Windows环境下比较有用，Windows下通常会有`cmd`和`powershell`两种`shell`，可能还会有`sh`。这时就可以通过`SHELL`来指定所使用的`shell`类型：

```
FROM microsoft/windowsservercore

# Executed as cmd /S /C echo default
RUN echo default

# Executed as cmd /S /C powershell -command Write-Host default
RUN powershell -command Write-Host default

# Executed as powershell -command Write-Host hello
SHELL ["powershell", "-command"]
RUN Write-Host hello

# Executed as cmd /S /C echo hello
SHELL ["cmd", "/S"", "/C"]
RUN echo hello
```