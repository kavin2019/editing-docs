## bash中括号的使用

包括一下几种：

* ${var}
* $(cmd)
* ()和{}
* $((exp))
* \${var:-string},\${var:+string},\${var:=string},${var:?string}
* \$(var%pattern),\$(var%%pattern),\$(var#pattern),\$(var##pattern)
* []和[[  ]]

### ${var}

变量调用

### $(cmd)

和`cmd`类似，执行linux命令。
注意：$(cmd)只输出标准输出，如果发生错误，则无输出

例如：

```shell
$ echo $(ls)
anaconda-ks.cfg b.log for1.sh gt.js server.sh
$ echo $(ls a)
ls: cannot access a: No such file or directory
  #这行的空白就是echo输出的，上面是ls执行命令的报错信息，而这里因为ls报错所以$(ls)没有输出
$ echo $(ls a 2>&1) #这里我将错误重定向到标准输出，就只有一行输出（echo的输出)
ls: cannot access a: No such file or directory
```



### 一串命令的执行()和{}

二者都用于执行一串命令，指令之间用；分开，注意：括号内的数据重导向只影响括号内部的指令输入输出，不影响括号外部的指令
不同之处：

1. 运行方式
   * ()重新打开一个子shell执行
   * {}在当前shell执行
2. 语法
   * ()最后一条指令不需要分号(;)，{}最后一条指令需要分号(;)
   * ()第一个指令和左括号不需要空格，{}第一个指令和左括号需要空格。（这个左右空格留白应该养成习惯）

例1：

```shell
[root@localhost ~]# var=test
[root@localhost ~]# echo $var
test
[root@localhost ~]# (var=change;echo $var)
change
[root@localhost ~]# echo $var
test
[root@localhost ~]# { var=change;echo $var }
> ^C
[root@localhost ~]# { var=change;echo $var; }
change
[root@localhost ~]# echo $var
change
```

例2:

```shell
[root@localhost ~]# pstree #当前环境进程树
systemd─┬─NetworkManager───2*[{NetworkManager}]
        ...
        ├─sshd─┬─sshd───bash───pstree #当前只有一个bash，即当前shell
        │      └─sshd───bash───redis-cli
        ...
        └─tuned───4*[{tuned}]
[root@localhost ~]# (pstree;var=123) #添加var=123赋值，防止子shell结束太快
systemd─┬─NetworkManager───2*[{NetworkManager}]
        ...
        ├─sshd─┬─sshd───bash───bash───pstree #比之前多了个bash，即新建的子shell
        │      └─sshd───bash───redis-cli
        ...
        └─tuned───4*[{tuned}]
[root@localhost ~]# { pstree;var=123;}
systemd─┬─NetworkManager───2*[{NetworkManager}]
        ...
        ├─sshd─┬─sshd───bash───pstree 
        │      └─sshd───bash───redis-cli
				...
        └─tuned───4*[{tuned}]
[root@localhost ~]# (pstree；)
-bash: pstree；: command not found

```

### 双小括号(())

双小括号运算符，**双圆括号结构是在bash中使用C语言风格变量操作的一种处理机制**，具有一下特征：

> 1. 所有表达式可以像c语言，如：a++,b-=1等，
> 2. 整数扩展，这种扩展是整数的计算，不支持浮点型。
> 3. 所有变量可以不加$
> 4. 可以进行逻辑运算和四则运算，如：1+3,1>0等
> 5. 支持多个表达式，通过逗号隔开
> 6. 双括号结构 扩展了for，while,if条件测试运算

### 

### \${var: } ,\${var%}用于变量的替换和匹配替换结构

替换：

* \${var:-string} 若var为空，\${var:-string}值为string  

* \${var:=string} 与${var:-string}功能类型，不过\${var:=string} 将string值赋给var变量

* \${var:=string} 判断某个变量是否赋值，没有的话则给它赋上一个默认值  

* \${var:+string}的替换规则和\${var:=string}相反，即只有当var不是空的时候才替换成string，若var为空时则不替换或者说是替换成变量 var的值，即空值

匹配：

* \${var%pattern}和\${var%%pattern}表示从最右边(即结尾)匹配的

* \${var#pattern} 和\${var##pattern}从最左边(即开头)匹配的

### 中括号[] [[]]

* bash 单中括号[]
  **基本要素：**

  * `[ ] 两个符号左右都要有空格分隔`

  * `内部操作符与操作变量之间要有空格：如  [  “a”  =  “b”  ]`

  * `字符串比较中，> < 需要写成\\> \\< 进行转义`

  * `[ ]中字符串或者${}变量尽量使用"" 双引号扩住，避免值未定义引用而出错的好办法`

  * `[ ] 中可以使用 –a –o 进行逻辑运算`

  * `[ ] 是bash 内置命令`

* bash中[[ ]]
  **基本要素：**

  * `[[ ]] 两个符号左右都要有空格分隔`

  * `内部操作符与操作变量之间要有空格：如  [[  “a” =  “b”  ]]`

  * `字符串比较中，可以直接使用 > < 无需转义`

  * `[[ ]] 中字符串或者${}变量尽量如未使用"" 双引号扩住的话，会进行模式和元字符匹配`
    ```
    $ [[ "ab"=a\* ]] && echo "ok"
		ok
    ```
  * `[[ ]] 内部可以使用 &&  || 进行逻辑运算`

  * `[[ ]] 是bash  keyword：[[ is a shell keyword`

  * [[ ]] 其他用法都和[ ] 一样

  * `[[ ]] 和 [ ] 都可以和 ! 配合使用`
    ```
    优先级 !  >  && > || 

    逻辑运算符  < 关系运算符

    逻辑运算符  ： !  &&  || -a  -o

    关系运算符  ： <  >  \> \<  ==  = !=  – eq –ne  -gt -ge  –lt  -le
    ```

### [[  ]] 比[ ] 具备的优势

​    ①[[是 bash 程序语言的关键字。并不是一个命令，[[ ]] 结构比[ ]结构更加通用。在[[和]]之间所有的字符都不会发生文件名扩展或者单词分割，但是会发生参数扩展和命令替换。

​    ②支持字符串的模式匹配，使用=~操作符时甚至支持shell的正则表达式。字符串比较时可以把右边的作为一个模式，而不仅仅是一个字符串，比如[[ hello == hell? ]]，结果为真。[[ ]] 中匹配字符串或通配符，不需要引号。

​    ③使用[[ ... ]]条件判断结构，而不是[... ]，能够防止脚本中的许多逻辑错误。比如，&&、||、<和> 操作符能够正常存在于[[ ]]条件判断结构中，但是如果出现在[ ]结构中的话，会报错。

​    ④bash把双中括号中的表达式看作一个单独的元素，并返回一个退出状态码。