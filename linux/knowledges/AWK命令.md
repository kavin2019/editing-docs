## 简介

> awk是一个强大的文本分析工具，相对于grep的查找，sed的编辑，awk在其对数据分析并生成报告时，显得尤为强大。简单来说awk就是把文件逐行的读入，以空格为默认分隔符将每行切片，切开的部分再进行各种分析处理。
>
> awk有3个不同版本: awk、nawk和gawk，未作特别说明，一般指gawk，gawk 是 AWK 的 GNU 版本
>
> awk语言的最基本功能是在文件或者字符串中基于指定规则浏览和抽取信息，awk抽取信息后，才能进行其他文本操作

## 使用方法

```shell
awk '{pattern + action}' {filenames}
pattern:表示AWK在数据中查找的内容，一般是正则表达式，需要用反斜杠括起来
action：表示AWK在找到匹配的内容后需要执行的一系列命令
花括号({})并不是一定需要，但它用于根据特定pattern执行一系列命令进行分组。
```

## 调用AWK

有三种方式调用

```
1.命令行方式
awk [-F  field-separator]  'commands'  input-file(s)
其中，commands 是真正awk命令，[-F域分隔符]是可选的。 input-file(s) 是待处理的文件。
在awk中，文件的每一行中，由域分隔符分开的每一项称为一个域。通常，在不指名-F域分隔符的情况下，默认的域分隔符是空格。

2.shell脚本方式
将所有的awk命令插入一个文件，并使awk程序可执行，然后awk命令解释器作为脚本的首行，一般通过键入脚本名称来调用。
相当于shell脚本首行的：#!/bin/sh
可以换成：#!/bin/awk

3.将所有的awk命令插入一个单独文件，然后调用：
awk -f awk-script-file input-file(s)
其中，-f选项加载awk-script-file中的awk脚本，input-file(s)跟上面的是一样的。
```

## 入门实例

* 使用范例

  ```
  [root@localhost ~]# last -n 5
  root     pts/0        192.168.254.104  Sat Jun  1 12:14   still logged in   
  root     pts/0        192.168.254.104  Sat Jun  1 01:50 - 04:56  (03:05)    
  root     pts/1        192.168.254.178  Thu May 30 17:49   still logged in   
  root     pts/2        192.168.254.104  Thu May 30 14:32 - 02:26  (11:53)    
  root     pts/2        192.168.254.104  Thu May 30 14:31 - 14:32  (00:01)    
  ```

* 只显示最近登录的ip

  ```
  [root@localhost ~]# last -n 5| awk '{print $3}'
  192.168.254.104
  192.168.254.104
  192.168.254.178
  192.168.254.104
  192.168.254.104
  
  Tue
  
  ```

  >awk的工作流程是：读入有'\n'换行符分割的一条记录（也就是读入一行内容），然后将记录按指定的域分隔符划分域（列），填充域，`$0则表示所有域`,`$1表示第一个域`,`$n表示第n个域`。默认域分隔符是"空白键" 或 "[tab]键",所以`$1表示登录用户`，`$3表示登录用户ip`,以此类推

* 指定分隔符

  ```
  #指定分隔符':'
  [root@localhost ~]# awk -F ':' '{print $1 "\t" $6}' /etc/passwd
  root	/root
  bin	/bin
  daemon	/sbin
  adm	/var/adm
  lp	/var/spool/lpd
  sync	/sbin
  shutdown	/sbin
  halt	/sbin
  mail	/var/spool/mail
  operator	/root
  games	/usr/games
  ftp	/var/ftp
  nobody	/
  systemd-network	/
  dbus	/
  polkitd	/
  sshd	/var/empty/sshd
  postfix	/var/spool/postfix
  chrony	/var/lib/chrony
  ntp	/etc/ntp
  nginx	/home/nginx
  oprofile	/var/lib/oprofile
  mysql	/home/mysql
  ```

* 输出头和尾

  ```
  [root@localhost ~]# last -n 5|awk 'BEGIN{print "user\tIP"}{print $1"\t"$3}END{print "output end!"}'
  user	IP
  root	192.168.254.104
  root	192.168.254.104
  root	192.168.254.178
  root	192.168.254.104
  root	192.168.254.104
  	
  wtmp	Tue
  output end!
  ```

  > BEGIN：固定写法，表示在AWK处理文件开始时进行的一系列命令。如输出表头，初始化变量等
  >
  > END： 固定写法，表示AWK处理完后执行的一系列命令

* 处理匹配模式
  查看root使用的shell

  ```
  [root@localhost ~]# awk -F ':' 'BEGIN {print "shell"} /root/{print $7}' /etc/passwd
  shell
  /bin/bash
  /sbin/nologin
  ```

  > /root/{print $7}即为`pattern+{action}`模式，处理行如果匹配pattern才会调用action命令

## 内置命令

awk内置了许多变量用于设置awk工作时的环境，这些变量可以改变：

```
ARGC               命令行参数个数(argument count)
ARGV               命令行参数排列(argument view)
ENVIRON            支持队列中系统环境变量的使用
FILENAME           awk浏览的文件名
FNR                浏览文件的记录数
FS                 设置输入域分隔符，等价于命令行 -F选项(file split)
NF                 浏览记录的域的个数(The Number OF Field)
NR                 已读的记录数（the number of rows)
OFS                输出域分隔符(output field split)
ORS                输出记录分隔符(output row split)
RS                 控制记录分隔符(row split)
```

## 打印命令

* print
  其中print函数的参数可以是变量、数值或者字符串。
  字符串必须用双引号引用，参数用逗号分隔。如果没有逗号，参数就串联在一起而无法区分。这里，逗号的作用与输出文件的分隔符的作用是一样的，只是后者是空格而已。

* printf
  linux内置命令，其用法等同与c语言中的语法。
  `printf format, arg [,arg..]`

  > **格式替代符**
  >
  > - %b 相对应的参数被视为含有要被处理的转义序列之字符串。
  > - %c ASCII字符。显示相对应参数的第一个字符
  > - %d, %i 十进制整数
  > - %e, %E, %f 浮点格式
  > - %g %e或%f转换，看哪一个较短，则删除结尾的零
  > - %G %E或%f转换，看哪一个较短，则删除结尾的零
  > - %o 不带正负号的八进制值
  > - %s 字符串
  > - %u 不带正负号的十进制值
  > - %x 不带正负号的十六进制值，使用a至f表示10至15
  > - %X 不带正负号的十六进制值，使用A至F表示10至15
  > - %% 字面意义的%
  >
  > **转义序列**(print同样意义)
  >
  > - \a 警告字符，通常为ASCII的BEL字符
  > - \b 后退
  > - \c 抑制（不显示）输出结果中任何结尾的换行字符（只在%b格式指示符控制下的参数字符串中有效），而且，任何留在参数里的字符、任何接下来的参数以及任何留在格式字符串中的字符，都被忽略
  > - \f 换页（formfeed）
  > - \n 换行
  > - \r 回车（Carriage return）
  > - \t 水平制表符
  > - \v 垂直制表符
  > - \\ 一个字面上的反斜杠字符
  > - \ddd 表示1到3位数八进制值的字符，仅在格式字符串中有效
  > - \0ddd 表示1到3位的八进制值字符

  ​	例如：

     ```shell
  #!/bin/bash
  
  printf "%-5s %-10s %-4s\n" NO Name Mark
  printf "%-5s %-10s %-4.2f\n" 01 Tom 90.3456
  printf "%-5s %-10s %-4.2f\n" 02 Jack 89.2345
  printf "%-5s %-10s %-4.2f\n" 03 Jeff 98.4323
     ```

  > - %-5s 格式为左对齐且宽度为5的字符串代替（-表示左对齐），不使用则是右对齐。
  > - %-4.2f 格式为左对齐宽度为4，保留两位小数。

​    

## AWK编程

* 使用变量

  ```shell
  [root@localhost ~]# awk -F ':' 'BEGIN{count=0} {count++;print $1} END{print count}' /etc/passwd
  root
  bin
  [...]中间省略
  oprofile
  mysql
  23
  ```

  > 在BEGIN中初始化变量count，虽然直接使用count默认从0开始(例如：`awk -F ':' '{count++;print $1} END{print count}' /etc/passwd`)，但是严谨的话还是在BEGIN中初始化。

* 统计文件夹中文件大小

  ```
  //默认单位为byte
  [root@localhost ~]# ls -l|awk 'BEGIN{size=0}{size += $5}END{print size}'
  10540
  //以kb统计
  [root@localhost ~]# ls -l|awk 'BEGIN{size=0}{size += $5}END{print size/1024 "KB"}'
  10.293KB
  //统计不包括子文件夹
  ```

* 支持条件结构（if…else）
  统计文件大小大于1M的文件

  ```
  [root@localhost ~]# ls -l | awk '{if($5>1*1024){printf "%s\n",$9;}}'
  anaconda-ks.cfg
  gt.js
  ```

* 支持循环和数组

  * 循环语句同样借鉴于C语言，支持while、do/while、for、break、continue

  * 因为awk中数组的下标可以是数字和字母，数组的下标通常被称为关键字(key)。值和关键字都存储在内部的一针对key/value应用hash的表格里。由于hash不是顺序存储，因此在显示数组内容时会发现，它们并不是按照你预料的顺序显示出来的。数组和变量一样，都是在使用时自动创建的，awk也同样会自动判断其存储的是数字还是字符串。一般而言，awk中的数组用来从记录中收集信息，可以用于计算总和、统计单词以及跟踪模板被匹配的次数等等。

  ```shell
  [root@localhost ~]# ls -l | awk 'BEGIN{count=0}{name[count]=$9;count++}END{for(i=0;i<count;i++){printf "%d %s\n",i,name[i]}}'
  0 
  1 anaconda-ks.cfg
  2 gt.js
  3 server.sh
  ```

  