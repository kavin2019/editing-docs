# dos2unix

***

## 简介

> **dos2unix命令**用来将DOS格式的文本文件转换成UNIX格式的（DOS/MAC to UNIX text fileformat converter）。DOS下的文本文件是以`\r\n`作为断行标志的，表示成十六进制就是0D 0A。而Unix下的文本文件是以\n作为断行标志的，表示成十六进制就是0A。DOS格式的文本文件在Linux底下，用较低版本的vi打开时行尾会显示`^M`，而且很多命令都无法很好的处理这种格式的文件，如果是个shell脚本，。而Unix格式的文本文件在Windows下用Notepad打开时会拼在一起显示。因此产生了两种格式文件相互转换的需求，对应的将UNIX格式文本文件转成成DOS格式的是unix2dos命令

## 格式

> dos2unix [-hkqV] [-c convmode] [-o file ...] [-n infile outfile ...]

## 选项

```
-k：保持输出文件的日期不变
-q：安静模式，不提示任何警告信息。
-V：查看版本
-c：转换模式，模式有：ASCII, 7bit, ISO, Mac, 默认是：ASCII。
-o：写入到源文件
-n：写入到新文件
```


## 例子：

