# nohup命令

> nohup 的全称是"no hung up"(不挂起)，用于在系统后台不挂断地运行命令，退出终端不会影响程序的运行
nohup 命令，在默认情况下（非重定向时），会输出一个名叫 nohup.out 的文件到当前目录下，如果当前目录的 nohup.out 文件不可写，输出重定向到 $HOME/nohup.out 文件中。

## 语法格式
```shell
$ nohup Command [ Arg … ] [　& ]
```
参数: 
+ Command: 需要执行的命令或者程序
+ Arg： 参数
+ &：让命令在后台执行，终端退出后命令仍旧执行。

## 实例

###  demo1: 让node程序在后台运行
```shell
$ nohup /usr/local/bin/node /var/www/nodejs/dev &
appending output to nohup.out //运行成功
```

