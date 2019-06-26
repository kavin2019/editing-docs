## bzip2压缩文件命令

### 简介

bzip2用于压缩文件，产生一个`.bzip2`的压缩包。

zip2压缩后会删除原来的文件，如果要保留使用选项`-k`。如果本地存在同名的压缩包，提示错误并停止压缩

zip2解压缩后会删除压缩包，如果要保留压缩包同样适用选项`-k`。如果存在原文件（或同名文件），提示错误并停止解压

### 格式

```shell
bzip2 [OPTIONS] filename
```

#### OPTIONS

```
-c或——stdout：将压缩与解压缩的结果送到标准输出；
-d或——decompress：执行解压缩；
-f或-force：bzip2在压缩或解压缩时，若输出文件与现有文件同名，预设不会覆盖现有文件。若要覆盖。请使用此参数；
-h或——help：在线帮助；
-k或——keep：bzip2在压缩或解压缩后，会删除原始文件。若要保留原始文件，请使用此参数；
-s或——small：降低程序执行时内存的使用量；
-t或——test：测试.bz2压缩文件的完整性；
-v或——verbose：压缩或解压缩文件时，显示详细的信息；
-z或——compress：强制执行压缩；
-V或——version：显示版本信息；
--repetitive-best：若文件中有重复出现的资料时，可利用此参数提高压缩效果；
--repetitive-fast：若文件中有重复出现的资料时，可利用此参数加快执行效果。
-<1-9> 以1-9九个等级压缩文件，9压缩率最高，1压缩速度最高
```

### 实例

1. 压缩文件

   ```shell
   $ bzip2 ./*.txt 
   $ ls
   a.txt.bz2  b.txt.bz2  c.txt.bz2  d.txt.bz2  e.txt.bz2  f.txt.bz2  subdir
   ```

   

2. 解压缩并保留压缩包(默认是删除压缩包，同样对压缩文件时-k能保留原文件)

   ```shell
   $ bzip2 -dk ./*.bz2
   $ ls
   a.txt  a.txt.bz2  b.txt  b.txt.bz2  c.txt  c.txt.bz2  d.txt  d.txt.bz2  e.txt  e.txt.bz2  f.txt  f.txt.bz2  subdir
   ```

3. 输出到标准输出中

   ```shell
   #将a.txt使用bzip2压缩然后输出到标准输出，标准输出重定向到ab.bzip2文件。相当于将a.txt打包成ab.bzip2
   $ bzip2 -vc a.txt > ab.bzip2
   #解压ab.bzip2，可以直接解压，也可以通过-c解压到自己命名的文件
   $ bzip2 -dvc ab.bzip2 > ab.txt
   ```

   