## gunzip解压缩命令

### 简介

gunzip命令用于解压gzip压缩的文件，等价于`gzip -d`命令。gunzip命令实际是gzip的一个硬连接，所以无论是压缩还是解压缩都可以通过gzip来实现。

### 格式

```shell
gunzip [OPTIONS] <压缩后的文件>
```

#### OPTIONS

```
-a或——ascii：使用ASCII文字模式；
-c或--stdout或--to-stdout：把解压后的文件输出到标准输出设备；
-f或-force：强行解开压缩文件，不理会文件名称或硬连接是否存在以及该文件是否为符号连接；
-h或——help：在线帮助；
-l或——list：列出压缩文件的相关信息；
-L或——license：显示版本与版权信息；
-n或--no-name：解压缩时，若压缩文件内含有原来的文件名称及时间戳记，则将其忽略不予处理；
-N或——name：解压缩时，若压缩文件内含有原来的文件名称及时间戳记，则将其回存到解开的文件上；
-q或——quiet：不显示警告信息；
-r或——recursive：递归处理，将指定目录下的所有文件及子目录一并处理；
-S或<压缩字尾字符串>或----suffix<压缩字尾字符串>：更改压缩字尾字符串；
-t或——test：测试压缩文件是否正确无误；
-v或——verbose：显示指令执行过程；
-V或——version：显示版本信息；
```



### 实例

解压缩文件

```shell
$ gunzip a.txt.gz 
```

### 有趣的实验

```shell
#gunzip解压一个普通文件，会提示没有.gz后缀
$ gunzip a.txt 
gzip: a.txt: unknown suffix -- ignored
#然后将a.txt改为a.txt.gz,再进行解压缩，会提示不是gzip压缩的格式
$ gunzip a.txt.gz
gzip: a.txt.gz: not in gzip format
#将a.txt改回原来名称，然后用gzip压缩成a.txt.gz。再修改压缩后的文件名为a.txt，进行解压缩时：
$ gunzip a.txt 
gzip: a.txt: unknown suffix -- ignored #提示和普通文件一样的。
#改回后能正常解压。
```

