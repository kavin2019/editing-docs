## zip解压缩文件命令

### 简介

zip命令可以用来解压缩文件和文件目录，其压缩后会产生一个.zip后缀的压缩文件。解压缩使用unzip命令

### 格式

```shell
zip [OPTIONS] 文件或目录
```

#### OPTIONS

```
-A：调整可执行的自动解压缩文件；
-b<工作目录>：指定暂时存放文件的目录；
-c：替每个被压缩的文件加上注释；
-d：从压缩文件内删除指定的文件；
-D：压缩文件内不建立目录名称；
-f：此参数的效果和指定“-u”参数类似，但不仅更新既有文件，如果某些文件原本不存在于压缩文件内，使用本参数会一并将其加入压缩文件中；
-F：尝试修复已损坏的压缩文件；
-g：将文件压缩后附加在已有的压缩文件之后，而非另行建立新的压缩文件；
-h：在线帮助；
-i<范本样式>：只压缩符合条件的文件；
-j：只保存文件名称及其内容，而不存放任何目录名称；
-J：删除压缩文件前面不必要的数据；
-k：使用MS-DOS兼容格式的文件名称；
-l：压缩文件时，把LF字符置换成LF+CR字符；
-ll：压缩文件时，把LF+cp字符置换成LF字符；
-L：显示版权信息；
-m：将文件压缩并加入压缩文件后，删除原始文件，即把文件移到压缩文件中；
-n<字尾字符串>：不压缩具有特定字尾字符串的文件；
-o：以压缩文件内拥有最新更改时间的文件为准，将压缩文件的更改时间设成和该文件相同；
-q：不显示指令执行过程；
-r：递归处理，将指定目录下的所有文件和子目录一并处理；
-S：包含系统和隐藏文件；
-t<日期时间>：把压缩文件的日期设成指定的日期；
-T：检查备份文件内的每个文件是否正确无误；
-u：更换较新的文件到压缩文件内；
-v：显示指令执行过程或显示版本信息；
-V：保存VMS操作系统的文件属性；
-w：在文件名称里假如版本编号，本参数仅在VMS操作系统下有效；
-x<范本样式>：压缩时排除符合条件的文件；
-X：不保存额外的文件属性；
-y：直接保存符号连接，而非该链接所指向的文件，本参数仅在UNIX之类的系统下有效；
-z：替压缩文件加上注释；
-$：保存第一个被压缩文件所在磁盘的卷册名称；
-<压缩效率>：压缩效率是一个介于1~9的数值。
```



### 实例

1. 压缩普通文件

   ```shell
   $ zip a.txt.zip a.txt
     adding: a.txt (deflated 89%)
   $ ls
   a.txt  a.txt.zip  b.txt  c.txt
   #原文件会保留，如果不需要保留原文件可以加选项-m
   $ zip demo.gzip ./*
     adding: a.txt (deflated 89%)
     adding: b.txt (stored 0%)
     adding: c.txt (stored 0%)
   #将多个文件压缩到同一个压缩包
   ```

   

2. 压缩目录的时候需要指定-r选项，否则，只是将目录压缩而不包含文件。

   ```shell
   $ ls
   a.txt  b.txt  c.txt  subdir
   $ zip demo1.zip  ./*
     adding: a.txt (deflated 89%)
     adding: b.txt (stored 0%)
     adding: c.txt (stored 0%)
     adding: subdir/ (stored 0%)
   $ unzip -v demo1.zip 
   Archive:  demo1.zip
    Length   Method    Size  Cmpr    Date    Time   CRC-32   Name
   --------  ------  ------- ---- ---------- ----- --------  ----
       6210  Defl:N      702  89% 06-25-2019 16:00 0ca0bb35  a.txt
          0  Stored        0   0% 06-25-2019 15:59 00000000  b.txt
          0  Stored        0   0% 06-25-2019 15:59 00000000  c.txt
          0  Stored        0   0% 06-25-2019 17:04 00000000  subdir/
   --------          -------  ---                            -------
       6210              702  89%                            4 files
   #目录subdir中是有跟当前目录下一样的a.txt b.txt c.txt的文件的，但是压缩后大小只有0size。解压缩后目录是空的
   
   $ zip -r demo2.zip ./*
     adding: a.txt (deflated 89%)
     adding: b.txt (stored 0%)
     adding: c.txt (stored 0%)
     adding: subdir/ (stored 0%)
     adding: subdir/c.txt (stored 0%)
     adding: subdir/a.txt (deflated 89%)
     adding: subdir/b.txt (stored 0%)
   $ unzip -v demo2.zip 
   Archive:  demo2.zip
    Length   Method    Size  Cmpr    Date    Time   CRC-32   Name
   --------  ------  ------- ---- ---------- ----- --------  ----
       6210  Defl:N      702  89% 06-25-2019 16:00 0ca0bb35  a.txt
          0  Stored        0   0% 06-25-2019 15:59 00000000  b.txt
          0  Stored        0   0% 06-25-2019 15:59 00000000  c.txt
          0  Stored        0   0% 06-25-2019 17:04 00000000  subdir/
          0  Stored        0   0% 06-25-2019 15:59 00000000  subdir/c.txt
       6210  Defl:N      702  89% 06-25-2019 16:00 0ca0bb35  subdir/a.txt
          0  Stored        0   0% 06-25-2019 15:59 00000000  subdir/b.txt
   --------          -------  ---                            -------
      12420             1404  89%                            7 files
    #所以打包压缩目录时需要指定-r选项
   ```

   

3. 删除压缩包中指定的文件

   ```shell
   $ zip demo2.zip -d a.txt
   deleting: a.txt
   $ unzip -v demo2.zip 
   Archive:  demo2.zip
    Length   Method    Size  Cmpr    Date    Time   CRC-32   Name
   --------  ------  ------- ---- ---------- ----- --------  ----
          0  Stored        0   0% 06-25-2019 15:59 00000000  b.txt
          0  Stored        0   0% 06-25-2019 15:59 00000000  c.txt
          0  Stored        0   0% 06-25-2019 15:59 00000000  subdir/c.txt
       6210  Defl:N      702  89% 06-25-2019 16:00 0ca0bb35  subdir/a.txt
          0  Stored        0   0% 06-25-2019 15:59 00000000  subdir/b.txt
   --------          -------  ---                            -------
       6210              702  89%                            5 files
   ```

   

4. 更新或加入文件

   ```shell
   $ zip -u  demo2.zip a.txt 
     adding: a.txt (deflated 89%)
   zip error: Invalid command arguments (short option 'V' not supported)
   $ unzip -v demo2.zip 
   Archive:  demo2.zip
    Length   Method    Size  Cmpr    Date    Time   CRC-32   Name
   --------  ------  ------- ---- ---------- ----- --------  ----
          0  Stored        0   0% 06-25-2019 15:59 00000000  b.txt
          0  Stored        0   0% 06-25-2019 15:59 00000000  c.txt
          0  Stored        0   0% 06-25-2019 15:59 00000000  subdir/c.txt
       6210  Defl:N      702  89% 06-25-2019 16:00 0ca0bb35  subdir/a.txt
          0  Stored        0   0% 06-25-2019 15:59 00000000  subdir/b.txt
       6210  Defl:N      702  89% 06-25-2019 16:00 0ca0bb35  a.txt 
   --------          -------  ---                            -------
      12420             1404  89%                            6 files
      #a.txt重新加入到压缩包中。
   ```

   

5. 