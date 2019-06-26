## tar打包命令

### 简介

tar命令用于将多个文件或目录打包备份成一个文件tar包（归档文件），可以在tar包中修改文件，或添加新的文件。tar命令将多个文件合并成一个文件有助于管理和将文件进行网络传输。

首先要弄清两个概念：打包和压缩。打包是指将一大堆文件或目录变成一个总的文件；压缩则是将一个大的文件通过一些压缩算法变成一个小文件。

为什么要区分这两个概念呢？这源于Linux中很多压缩程序只能针对一个文件进行压缩，这样当你想要压缩一大堆文件时，你得先将这一大堆文件先打成一个包（tar命令），然后再用压缩程序进行压缩（[gzip](http://man.linuxde.net/gzip) [bzip2](http://man.linuxde.net/bzip2)命令）。

### 格式

```shell
tar [OPTIONS] 文件或目录 [文件或目录...]
```

#### OPTIONS

```
-A或--catenate：新增文件到以存在的备份文件；
-B：设置区块大小；
-c或--create：建立新的备份文件；
-C <目录>：这个选项用在解压缩，若要在特定目录解压缩，可以使用这个选项。
-d：记录文件的差别；
-x或--extract或--get：从备份文件中还原文件；
-t或--list：列出备份文件的内容；
-z或--gzip或--ungzip：通过gzip指令处理备份文件；
-Z或--compress或--uncompress：通过compress指令处理备份文件；
-f<备份文件>或--file=<备份文件>：指定备份文件；
-v或--verbose：显示指令执行过程；
-r：添加文件到已经压缩的文件；
-u：添加改变了和现有的文件到已经存在的压缩文件；
-j：支持bzip2解压文件；
-v：显示操作过程；
-l：文件系统边界设置；
-k：保留原有文件不覆盖；
-m：保留文件不被覆盖；
-w：确认压缩文件的正确性；
-p或--same-permissions：用原来的文件权限还原文件；
-P或--absolute-names：文件名使用绝对名称，不移除文件名称前的“/”号；
-N <日期格式> 或 --newer=<日期时间>：只将较指定日期更新的文件保存到备份文件里；
--exclude=<范本样式>：排除符合范本样式的文件。
```

### 拓展

一般选项中这几个选项不会同时出现：

```shell
-c 创建tar包
-x 解包
-t 查看包内容
-u 更新或添加新文件或目录
-r 添加新文件或目录
```

必选选项

```shell
-f 指定文件。#而且要注意的是多个选项是，f选项必须在最后。如 '-zcfv'是不允许的，应该是'-zcvf'
```

可选选项

```shell
-v 显示创建/解压tar包和压缩时的过程信息
-z 创建tar包后使用gzip压缩包/解压tar包前使用gzip解压文件
-j 同-z，只是使用bzip2压缩和解压缩
-p 属性，在tar命令打包和压缩时保留文件的原来属性。这个在备份系统文件时十分重要。
#解压缩可选，目录必须存在
-C <目录>：这个选项用在解压缩，若要在特定目录解压缩，可以使用这个选项。
```



### 实例

1. 打包文件不压缩

   ```shell
   #打包文件和目录
   $ tar -cvf tar.package ./*
   ./a.txt
   ./b.txt
   ./c.txt
   ./subdir/
   ./subdir/c.txt
   ./subdir/a.txt
   ./subdir/b.txt
   
   #查看tar包中文件和目录
   $ tar -tvf tar.package 
   -rw-rw-r-- demo1/demo1    6210 2019-06-25 16:00 ./a.txt
   -rw-rw-r-- demo1/demo1       0 2019-06-25 15:59 ./b.txt
   -rw-rw-r-- demo1/demo1       0 2019-06-25 15:59 ./c.txt
   drwxr-xr-x root/root         0 2019-06-25 17:04 ./subdir/
   -rw-rw-r-- root/root         0 2019-06-25 15:59 ./subdir/c.txt
   -rw-rw-r-- root/root      6210 2019-06-25 16:00 ./subdir/a.txt
   -rw-rw-r-- root/root         0 2019-06-25 15:59 ./subdir/b.txt
   
   #解包到指定目录（必须存在），不指定则解压到当前目录
   $ mkdir package
   $ tar -xvf tar.package -C package
   ./a.txt
   ./b.txt
   ./c.txt
   ./subdir/
   ./subdir/c.txt
   ./subdir/a.txt
   ./subdir/b.txt
   #从tar包中添加或更新文件
   $ tar -uvf tar.package d.txt 
   d.txt
   $ tar -tvf tar.package d.txt 
   -rw-r--r-- root/root         0 2019-06-25 17:51 d.txt
   $ tar -tvf tar.package       
   -rw-rw-r-- demo1/demo1    6210 2019-06-25 16:00 ./a.txt
   -rw-rw-r-- demo1/demo1       0 2019-06-25 15:59 ./b.txt
   -rw-rw-r-- demo1/demo1       0 2019-06-25 15:59 ./c.txt
   drwxr-xr-x root/root         0 2019-06-25 17:04 ./subdir/
   -rw-rw-r-- root/root         0 2019-06-25 15:59 ./subdir/c.txt
   -rw-rw-r-- root/root      6210 2019-06-25 16:00 ./subdir/a.txt
   -rw-rw-r-- root/root         0 2019-06-25 15:59 ./subdir/b.txt
   -rw-r--r-- root/root         0 2019-06-25 17:51 d.txt
   ```

   

2. 如果在创建或者解压tar包时压缩，只要加上-z或-j选项即可。如：

   ```shell
   创建压缩包： tar -zcvf tar.package ./*
   解压压缩包： tar -zxvf tar.package
   查看压缩包： tar -ztvf tar.package 
   更新或添加新文件: tar -zuvf tar.package 新文件
   ```

   