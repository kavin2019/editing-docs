## tee 重定向到文件并显示在屏幕

### 简介

**tee命令**用于将数据重定向到文件，另一方面还可以提供一份重定向数据的副本作为后续命令的stdin。简单的说就是把数据重定向到给定文件和屏幕上

存在缓存机制，每1024个字节将输出一次。若从管道接收输入数据，应该是缓冲区满，才将数据转存到指定的文件中。若文件内容不到1024个字节，则接收完从标准输入设备读入的数据后，将刷新一次缓冲区，并转存数据到指定文件

### 格式

> tee <OPTIONS> <filename>

filename:输出文件

### OPTIONS

```shell
-a: 向文件输出时，使用追加模式
-i：忽略中断信号
```



### 例题：

```shell
#在终端打印stdout同时重定向到文件中
$ ls | tee out.txt
1.sh
1.txt
2.txt
eee.tst
EEE.tst
one
out.txt
string2
www.pdf
WWW.pdf
WWW.pef
```