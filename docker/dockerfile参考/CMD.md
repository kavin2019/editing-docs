## CMD容器启动命令

CMD指令和RUN指令一样有两种格式:

```shell
shell格式:CMD <命令>
exec格式:CMD ["可执行程序", "参数1", "参数2"...]
参数列表格式：CMD["参数1", "参数2"...]。在指定了ENTRYPOINT指令后，使用CMD指定具体的参数
```

CMD指令用于指定在容器启动时运行的默认命令及参数。在`docker run`运行容器时可以指定新的命令来代替这个默认命令。例如：

```shell
#在运行centos系统时，默认的命令是/bin/bash。现在我们用输出命令echo来代替
docker run -it centos echo 'hello docker'
```

在指令格式上，一般推荐exec格式，这类格式在解析时会被解析成json数组，因此一定要用双引号，而不是单引号。如果使用shell格式的话，实际命令会被包装为`sh -c` 的参数的形式执行。例如：

```shell
CMD echo $home
#在实际执行中，会将其变更为:
CMD ["sh", "-c", "echo $home"]
#这就是我们为什么能使用环境变量的原因，因为这些环境变量会被shell进行解析处理。
```

在Dockfile中只会保留最有一个CMD作为容器启动的默认命令，然后容器运行时指定的命令可以替换默认的命令

注意：exec格式，不会触发shell，所以`$HOME`这样的环境变量无法使用，但它可以在没有`bash`的镜像中执行，而且可以避免错误的解析命令字符串。上面例题中指定了sh，所以能够使用环境变量。

### 拓展

> docker 不是虚拟机，容器内的程序都应该是前台程序（或者说至少有一个前台程序），而不能像虚拟机和物理机一样，用`systemctl`启动后台服务。
>
> 对于容器而言，启动程序就是容器的主程序，容器是为了主程序而存在的，如果主程序退出了那么容器也就没有存在的意义了，从而自动退出，其它辅助进程不是它关心的东西。
>
> 例如：
>
> ```shell
> #直接在容器中启动nginx,dokcer容器执行完后会直接退出
> CMD systemctl start nginx
> ```
>
> 以`systemctl start`方式启动nginx是系统以后台守护进程的方式来运行nginx服务。而`systemctl start nginx`实际被包装成CMD ["sh", "-c","systemctl start nginx"]的形式，因此主进程实际是sh，那么当`systemctl start nginx`命令完成后，sh进程也就结束后退出了，那么容器也会跟着退出。
>
> 正确的做法是直接执行nginx，并要求以前台的形式运行
>
> ```shell
> CMD["nginx", "-g", "daemon off;"]
> ```
>
> 