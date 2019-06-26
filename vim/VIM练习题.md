1、将/etc/fstab文件中第二列单独存到/home/a.sh文件中

```shell
#方法一
$ cat /etc/fstab | grep -v -E '^#|^\s*$' | awk '{printf $2 "\n"}' > /home/a.sh
$ cat /home/a.sh 
/
/boot
/home
swap
```



2、设置自己vim环境查找高亮反白、语法检验、行号显示、两个状态栏

```shell
#查找高亮
set hlsearch
#语法检查
syntax on
#行号显示
set number
#两个状态栏
set laststatus=2
```



3、使用vim打开文件后，在第34行向右移动15个字符，写出命令

```shell
34G 15l
```

4、vim打开文件，如何去该文件页首与页尾

```shell
？？？？
gg dd
G dd
```



5、vim打开文件，如何去行首与行尾

6、vim一般模式下r的功能

7、将vim正在编辑的文件另存为新文件

8、如何进入vim编辑模式

9、如何由编辑模式跳回一般模式

10、vim中如果上下左右键无法使用那么如何在一般模式中移动光标

11、在vim一般模式中删除1行，n行，删除到第n行，删除一个字符

12、在vim一般模式中复制1行，n行，复制到第n行并加以粘贴

13、在vim中如何查找string这个字符串

14、在vim中将string1替换成string2，如果需要确认机制如何做

15、vim中如何读取一个文件进入当前文件

16、vim下进行很多编辑后如何还原成原来的文件内容

17、在vim下不离开vim执行ls/home这个命令

18、将a.sh改成dos格式

19、修改a.sh文件的编码为GBK

20、打开/etc/vim.config文件，前后对比其内容

21、将a.sh文件第二列内容复制到b.sh文件中

模拟：

1、vim打开a.sh文件，将其放入后台，查看是否存在a.sh.swp文件，删除后台vim任务，恢复原文件

2、复制/etc/man.config文件到当前目录，使用vim打开，设置行号，移动到第58行，向右移动40个字符，查看双引号内容；移动到下一行，向下查找"bzip2"字符串，看在第几行；将50-100行之间的“man”改成“MAN”，查看修改个数；恢复刚才的修改；复制65-73内容粘贴到最后一行；21-42行间开头尾"#"符号的批注全部删除；将文另存为man.test.config；到27行删除7个字符；在第一行添加"lam is a student"；保存离开