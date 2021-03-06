## compress压缩文件

### 简介

**compress命令**使用“Lempress-Ziv”编码压缩数据文件，压缩文件后多一个`.Z`的后缀。

### 格式

```shell
compress [OPTIONS] filename [filename ...]
```

#### OPTIONS

```
-f：不提示用户，强制覆盖掉目标文件；
-c：将结果送到标准输出，无文件被改变；
-r：递归的操作方式；
-b<压缩效率>：压缩效率是一个介于9~16的数值，预设值为"16"，指定愈大的数值，压缩效率就愈高；
-d：对文件进行解压缩而非压缩；
-v：显示指令执行过程；
-V：显示指令版本及程序预设值。
```
### 拓展
解压缩命令`uncompress`等价于`compress -d`