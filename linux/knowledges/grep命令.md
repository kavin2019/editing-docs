## grep命令

> **grep**（global search regular expression(RE) and print out the line，全面搜索正则表达式并把行打印出来）是一种强大的文本搜索工具，它能使用正则表达式搜索文本，并把匹配的行打印出来

### 格式

```
grep [OPTIONS] pattern file1 [file2...]
```

### 常用选项

```
-a 不要忽略二进制数据。
-A<显示列数> 除了显示符合范本样式的那一行之外，并显示该行之后的内容。
-b 在显示符合范本样式的那一行之外，并显示该行之前的内容。
-c 计算符合范本样式的列数。
-C<显示列数>或-<显示列数>  除了显示符合范本样式的那一列之外，并显示该列之前后的内容。
-d<进行动作> 当指定要查找的是目录而非文件时，必须使用这项参数，否则grep命令将回报信息并停止动作。
-e<范本样式> 指定字符串作为查找文件内容的范本样式。
-E 将范本样式为延伸的普通表示法来使用，意味着使用能使用扩展正则表达式。
-f<范本文件> 指定范本文件，其内容有一个或多个范本样式，让grep查找符合范本条件的文件内容，格式为每一列的范本样式。
-F 将范本样式视为固定字符串的列表。
-G 将范本样式视为普通的表示法来使用。
-h 在显示符合范本样式的那一列之前，不标示该列所属的文件名称。
-H 在显示符合范本样式的那一列之前，标示该列的文件名称。
-i 忽略字符大小写的差别。
-l 列出文件内容符合指定的范本样式的文件名称。
-L 列出文件内容不符合指定的范本样式的文件名称。
-n 在显示符合范本样式的那一列之前，标示出该行的编号。
-q 不显示任何信息。
-R/-r 此参数的效果和指定“-d recurse”参数相同。
-s 不显示错误信息。
-v 反转查找。
-w 只显示全字符合的列。
-x 只显示全列符合的列。
-y 此参数效果跟“-i”相同。
-o 只输出文件中匹配到的部分。
```

### 范例

查询包含root的行

```shell
[root@91a968d13f58 src]# ps aux | grep root
root         1  0.0  0.1  11828  2880 pts/0    Ss   02:43   0:00 /bin/bash
root        40  0.0  0.1  51748  3400 pts/0    R+   03:23   0:00 ps aux
root        41  0.0  0.0  11828   388 pts/0    R+   03:23   0:00 /bin/bash
```

查询不包含bin的行

```shell
[root@91a968d13f58 src]# ps aux | grep -v bin
USER       PID %CPU %MEM    VSZ   RSS TTY      STAT START   TIME COMMAND
root        43  0.0  0.1  51748  3484 pts/0    R+   03:23   0:00 ps aux
```

在多个文件中查找

```shell
[root@91a968d13f58 src]# grep root /etc/password
```

标示颜色

```shell
[root@91a968d13f58 src]# grep root /etc/passwd --color=auto #自动标示匹配的字符颜色
root:x:0:0:root:/root:/bin/bash
operator:x:11:0:operator:/root:/sbin/nologin
[root@91a968d13f58 src]# grep root /etc/passwd --color=none #不用颜色标示
root:x:0:0:root:/root:/bin/bash
operator:x:11:0:operator:/root:/sbin/nologin
```

使用正则表达式

```shell
[root@91a968d13f58 src]# grep -E 'root|mail' /etc/passwd
root:x:0:0:root:/root:/bin/bash
mail:x:8:12:mail:/var/spool/mail:/sbin/nologin
operator:x:11:0:operator:/root:/sbin/nologin
[root@91a968d13f58 src]# egrep 'root|main' /etc/passwd #这种方式和grep -E 是等价的
root:x:0:0:root:/root:/bin/bash
operator:x:11:0:operator:/root:/sbin/nologin
```

只输出匹配行的匹配的字符串部分

```shell
[root@91a968d13f58 src]# egrep '\br.*t?'  /etc/passwd
root:x:0:0:root:/root:/bin/bash
operator:x:11:0:operator:/root:/sbin/nologin
[root@91a968d13f58 src]# egrep -o '\br.*t?'  /etc/passwd
root:x:0:0:root:/root:/bin/bash
root:/sbin/nologin
```

统计匹配的行数

```shell
[root@91a968d13f58 src]# egrep -c '\br.*t?'  /etc/passwd
2
```

查看匹配字符在整个文件或流中的偏移

```shell
[root@91a968d13f58 src]# ps aux | grep root -b
73:root         1  0.0  0.1  11828  2892 pts/0    Ss   02:43   0:00 /bin/bash
148:root        71  0.0  0.1  51748  3392 pts/0    R+   03:45   0:00 ps aux
220:root        72  0.0  0.0   9096   832 pts/0    S+   03:45   0:00 grep --color=auto root -b
```

查看匹配字符在哪些文件中

```shell
[root@91a968d13f58 src]# grep root test.txt /etc/passwd
test.txt:root         1  0.0  0.1  11828  2892 pts/0    Ss   02:43   0:00 /bin/bash
test.txt:root        75  0.0  0.1  51748  3472 pts/0    R+   05:51   0:00 ps aux
/etc/passwd:root:x:0:0:root:/root:/bin/bash
/etc/passwd:operator:x:11:0:operator:/root:/sbin/nologin
[root@91a968d13f58 src]# grep -l root test.txt /etc/passwd
test.txt
/etc/passwd
```

忽略大小写

```shell
[root@91a968d13f58 src]# grep  "Root" /etc/passwd
[root@91a968d13f58 src]# grep -i "Root" /etc/passwd
root:x:0:0:root:/root:/bin/bash
operator:x:11:0:operator:/root:/sbin/nologin
```

使用-e匹配多个字符

```shell
[root@91a968d13f58 src]# grep -e "root" -e "mail" /etc/passwd
root:x:0:0:root:/root:/bin/bash
mail:x:8:12:mail:/var/spool/mail:/sbin/nologin
operator:x:11:0:operator:/root:/sbin/nologin
```

grep静默输出

```shell
[root@91a968d13f58 src]# grep -q root /etc/passwd
[root@91a968d13f58 src]# echo $?
0
[root@91a968d13f58 src]# grep -q rooT /etc/passwd
[root@91a968d13f58 src]# echo $?
1

#不会输出任何信息，如果命令运行成功返回0，失败则返回非0值。一般用于条件测试。
```

对目录中文件进行递归匹配

```shell
[root@91a968d13f58 src]# grep root -rn . 
./test.txt:2:root         1  0.0  0.1  11828  2892 pts/0    Ss   02:43   0:00 /bin/bash
./test.txt:3:root        75  0.0  0.1  51748  3472 pts/0    R+   05:51   0:00 ps aux

#-r 指定对目录中的文件
#-n 输出行号
```

在grep搜索结果中包括或者排除指定文件：

```shell
#只在目录中所有的.php和.html文件中递归搜索字符"main()"
grep "main()" . -r --include *.{php,html}

#在搜索结果中排除所有README文件
grep "main()" . -r --exclude "README"

#在搜索结果中排除filelist文件列表里的文件
grep "main()" . -r --exclude-from filelist
```

打印grep匹配之前或之后的行

```shell
#显示匹配某个结果之后的3行，使用 -A 选项：
seq 10 | grep "5" -A 3
5
6
7
8

#显示匹配某个结果之前的3行，使用 -B 选项：
seq 10 | grep "5" -B 3
2
3
4
5

#显示匹配某个结果的前三行和后三行，使用 -C 选项：
seq 10 | grep "5" -C 3
2
3
4
5
6
7
8

#如果匹配结果有多个，会用“--”作为各匹配结果之间的分隔符：
echo -e "a\nb\nc\na\nb\nc" | grep a -A 1
a
b
--
a
b
```

