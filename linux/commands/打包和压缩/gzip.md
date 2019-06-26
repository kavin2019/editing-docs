## gzip压缩命令

### 简介

gzip命令用于压缩文件，gzip压缩文件的方式应用比较广泛（其中原因之一在内置在`tar`命令内，通过`-z`选项可以指定打包后用gzip压缩），其压缩后的文件具有`.gz`的后缀。

gzip命令对文本文件有60%～70%的压缩率。减少文件大小有两个明显的好处，一是可以减少存储空间，二是通过网络传输文件时，可以减少传输的时间。

### 格式

```shell
gzip [OPTIONS] filename
```

#### OPTIONS

```
-a或——ascii：使用ASCII文字模式；
-d或--decompress或----uncompress：解开压缩文件；
-f或——force：强行压缩文件。不理会文件名称或硬连接是否存在以及该文件是否为符号连接；
-h或——help：在线帮助；
-l或——list：列出压缩文件的相关信息；
-L或——license：显示版本与版权信息；
-n或--no-name：压缩文件时，不保存原来的文件名称及时间戳记；
-N或——name：压缩文件时，保存原来的文件名称及时间戳记；
-q或——quiet：不显示警告信息；
-r或——recursive：递归处理，将指定目录下的所有文件及子目录一并处理；
-S或<压缩字尾字符串>或----suffix<压缩字尾字符串>：更改压缩字尾字符串；
-t或——test：测试压缩文件是否正确无误；
-v或——verbose：显示指令执行过程；
-V或——version：显示版本信息；
-<压缩效率>：压缩效率是一个介于1~9的数值，预设值为“6”，指定愈大的数值，压缩效率就会愈高；
--best：此参数的效果和指定“-9”参数相同；
--fast：此参数的效果和指定“-1”参数相同。
```



### 拓展

1. gzip命令只能压缩文件，不能压缩文件夹。如果filename是文件夹路径，那么会提示错误。如果需要压缩文件夹，可以先通过tar命令打包后再压缩。

```shell
$ gzip ./
gzip: ./ is a directory -- ignored

# gzip -r ./ 可以递归的压缩当前目录及子目录下的所有文件，目录仍旧保留。
```

2. gzip压缩后不会保留原文件。

### 实例

压缩当前目录下的所有文件

```shell
$ ls
a.txt  b.txt  c.txt  d.txt
$ gzip ./*
$ ls
a.txt.gz  b.txt.gz  c.txt.gz  d.txt.gz
#压缩后不会保留原文件，压缩后文件后缀'.gz'
```

查看压缩文件的信息

```shell
$ gzip -l a.txt.gz 
         compressed        uncompressed  ratio uncompressed_name
                726                6210  88.7% a.txt
         #压缩前文件大小      压缩后文件大小  压缩率  压缩前文件名
```

解压缩命令

```shell
#解压单个压缩包
$ gzip -dv a.txt.gz 
a.txt.gz:	 88.7% -- replaced with a.txt

#gzip -drv ./* 递归解压缩当前目录及子目录下的所有压缩包
```

