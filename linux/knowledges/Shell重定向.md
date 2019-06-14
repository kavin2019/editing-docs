# Shell重定向

***

## 重定向介绍

在说重定向之前，先需要了解为什么需要进行重定向。

> 默认情况下，在执行交互式程序时都会接受参数然后输出结果，一般接受参数来自于键盘，这个就是输入；然后命令执行后返回结果，结果传递给显示器或者其它终端文件，这个就是输出。linux执行命令默认输入是键盘，输出分为标准输出和错误输出，而目标都是屏幕。在一些特殊情况会需要改变输入数据的来源和结果的输出目标，例如：一个后台命令在运行时用户希望它运行过程中的提示不要显示而是保存到一个文件中，这时可以将输出进行重定向。

## 文件描述符

在linux系统中有一个重要的法则`"一切皆文件"`，所以无论是设备还是存储数据的文件都被当成文件；而所有的文件在打开时，系统会分配一个索引号用于实现文件的快速定位，而这个索引号也叫文件描述符，它指向被打开的文件。

在shell程序启动时，会打开0，1，2三个索引号的文件，它们分别指向键盘，屏幕和屏幕，没错屏幕文件被打开了两次，而且不同索引号可以指向同一文件。

## 输出重定向

输出重定向通过> 和>> 来完成

```
语法： command [n] > filename
      command [n] >> filename
command:平常用的命令和shell脚本
n : 可选参数，一般1和2，表示标准输出和错误输出
> : 输出，文件如果存在，内容会被覆盖，不存在则创建后写入
>> : 输出，文件如果存在直接在文件末尾附加，否则创建后写入
filename： 文件名，也可以是其它设备或文件描述符(&n)
```

例如：

```shell
#标准输出
[admin@localhost shellscrip]$ ls
arithmetic.sh  first.sh  readonlyvar.sh  str.sh  tranpara.sh
[admin@localhost shellscrip]$ ls 1>a.txt #输出重定向,指定文件描述符为标准输出
[admin@localhost shellscrip]$ cat a.txt
arithmetic.sh
a.txt
first.sh
readonlyvar.sh
str.sh
tranpara.sh

#将内容以附加形式重定向，省略了文件描述符
[admin@localhost shellscrip]$ echo 'append other messages' >> a.txt 
[admin@localhost shellscrip]$ cat a.txt
arithmetic.sh
a.txt
first.sh
readonlyvar.sh
str.sh
tranpara.sh
append other messages  #添加的内容

#错误输出重定向
[admin@localhost shellscrip]$ cat b.txt 2>a.txt #错误重定向需要指定文件描述符2
[admin@localhost shellscrip]$ cat a.txt
cat: b.txt: No such file or directory  # a.txt文件内容被覆盖
#错误内容以附加的形式写入
[admin@localhost shellscrip]$ cat b.txt 2>> a.txt
[admin@localhost shellscrip]$ cat a.txt
cat: b.txt: No such file or directory
cat: b.txt: No such file or directory
#将错误输出重定向到标准输入
[admin@localhost ~]$ cat b.txt
cat: b.txt: No such file or directory
#定向到标准输出时使用的文件描述符，需要加&
#重定向时需要注意顺序，1>a.txt  2>&1 是先重定向标准输出，然后将错误输出重定向；最终1和2的输出是一样的
#但如果是 2>&1 1>a.txt时，下方a.txt文件将无内容，因为2先重定向到1时1指向屏幕，然后重定向1到b.txt文件，但不会影响2的重定向。所以错误输出会直接输出到屏幕
[admin@localhost ~]$ cat b.txt 1>a.txt  2>&1  
[admin@localhost ~]$ cat a.txt
cat: b.txt: No such file or directory
```

特殊的重定向，有时候不需要的输出消息可以被忽略，可以将输出进行一下定向：

```
command [n]> &-
command [n]> /dev/null #linux上一个黑洞设备，能吞噬所有传递给它的消息
```



## 输入重定向

输入重定线使用符号<来完成

```
语法： command [n]<filename
			command [n]<< end
			n : 可选参数，标准输入的文件描述符0
      < : 将filename文件内容输入
      << : 继续接受原来的输入，直到遇到end符号则结束输入。
      filename： 文件名，也可以是其它设备或文件描述符(&n)
      end： 符号串表示输入结束符号，代替EOF
```

在例子之前先介绍下cat，cat命令是接受输入然后将输出；默认情况下如果不指定文件，cat接受键盘输入然后输出到屏幕

```shell
[admin@localhost ~]$ cat 
123 #输入
123 #输出
456 #输入
456 #输出
    #这个位置实际上还有输入ctrl+d，输入EOF，表示文件结束符
```

例如：

```shell
[admin@localhost ~]$ cat input
hello shell
[admin@localhost ~]$ cat output
cat: output: No such file or directory
[admin@localhost ~]$ cat >output <input #现将输出重定向到output文件，再将输入定向input
[admin@localhost ~]$ cat output
hello shell
# 重新定义结束符为stop
[admin@localhost ~]$ cat <<stop
> 123
> 455
> stop #输入stop表示输入结束，cat就会进行输出
123
455
```



## 绑定重定向

语法：exec [n]>file或&文件描述符

绑定重定向是指将输入或输出在重定向后，一直保持重定向的方向。上面重定向在命令执行后就会失效

例如：

```shell
# 系统默认会打开下面几种描述符，都是已经在使用的
[admin@localhost ~]$ ls /proc/self/fd
0  1  2  3
# 将描述符6重定向，指向1。这一步很重要，用于保存1最初的指向。要不然后面可能就设不回1的定向了
[admin@localhost ~]$ exec 6>&1
# 系统增加了为6的文件描述符
[admin@localhost ~]$ ls /proc/self/fd
0  1  2  3  6
# 将1重定位为输入到a.txt文件
[admin@localhost ~]$ exec 1>a.txt
# 后续所有输出都不会显示在屏幕而是输出到a.txt文件了
[admin@localhost ~]$ ls /proc/self/fd
# 改回1的重定向
[admin@localhost ~]$ exec 1>&6
[admin@localhost ~]$ ls /proc/self/fd
0  1  2  3  6
# 将6重定向到&-，类似于关闭
[admin@localhost ~]$ exec 6>&-
[admin@localhost ~]$ ls /proc/self/fd
0  1  2  3
```

