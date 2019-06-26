## unzip解压zip压缩包

### 简介

unzip命令用于解压缩zip命令压缩后的文件。

### 语法

```shell
unzip [OPTIONS] 压缩包.zip
```

#### OPTIONS

```
-c：将解压缩的结果显示到屏幕上，并对字符做适当的转换；
-f：更新现有的文件；
-l：显示压缩文件内所包含的文件；
-p：与-c参数类似，会将解压缩的结果显示到屏幕上，但不会执行任何的转换；
-t：检查压缩文件是否正确；
-u：与-f参数类似，但是除了更新现有的文件外，也会将压缩文件中的其他文件解压缩到目录中；
-v：执行时显示详细的信息；
-z：仅显示压缩文件的备注文字；
-a：对文本文件进行必要的字符转换；
-b：不要对文本文件进行字符转换；
-C：压缩文件中的文件名称区分大小写；
-j：不处理压缩文件中原有的目录路径；
-L：将压缩文件中的全部文件名改为小写；
-M：将输出结果送到more程序处理；
-n：解压缩时不要覆盖原有的文件；
-o：不必先询问用户，unzip执行后覆盖原有的文件；
-P<密码>：使用zip的密码选项；
-q：执行时不显示任何信息；
-s：将文件名中的空白字符转换为底线字符；
-V：保留VMS的文件版本信息；
-X：解压缩时同时回存文件原来的UID/GID；
-d<目录>：指定文件解压缩后所要存储的目录；
-x<文件>：指定不要处理.zip压缩文件中的哪些文件；
-Z：unzip-Z等于执行zipinfo指令。
```

### 实例

1. 查看zip包中的文件

   ```shell
   $  unzip -v demo.gzip 
   Archive:  demo.gzip
    Length   Method    Size  Cmpr    Date    Time   CRC-32   Name
   --------  ------  ------- ---- ---------- ----- --------  ----
       6210  Defl:N      702  89% 06-25-2019 16:00 0ca0bb35  a.txt
          0  Stored        0   0% 06-25-2019 15:59 00000000  b.txt
          0  Stored        0   0% 06-25-2019 15:59 00000000  c.txt
   --------          -------  ---                            -------
       6210              702  89%                            3 files
   ```

2. 解压zip包到指定目录。（和压缩一样，解压缩不会删除压缩包）

   ```shell
   $ unzip -d subdir/ demo.gzip 
   Archive:  demo.gzip
     inflating: subdir/a.txt            
    extracting: subdir/b.txt            
    extracting: subdir/c.txt            
   $ ls
   a.txt  b.txt  c.txt  demo.gzip  subdir
   $ ls subdir/
   a.txt  b.txt  c.txt
   ```

3. 通过-n选项解压时如果文件存在，则不解压改文件

   ```shell
   [root@e675e3d56e94 demo1]# unzip -nd subdir/ demo.gzip 
   Archive:  demo.gzip
     inflating: subdir/a.txt 
   #a.txt已经被我删除了，b.txt和c.txt存在，所以不解压
   ```

4. unzip不检查压缩包后缀

   ```shell
   $ unzip -d demo2/  demo2.txt       
   Archive:  demo2.txt
    extracting: demo2/b.txt             
    extracting: demo2/c.txt             
    extracting: demo2/subdir/c.txt      
     inflating: demo2/subdir/a.txt      
    extracting: demo2/subdir/b.txt      
     inflating: demo2/a.txt  
   ```

   