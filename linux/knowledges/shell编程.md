# Shell编程

***

什么是Shell
>Shell 是一个用 C 语言编写的程序，它是用户使用 Linux 的桥梁。Shell 既是一种命令语言，又是一种程序设计语言。
>Shell 是指一种应用程序，这个应用程序提供了一个界面，用户通过这个界面访问操作系统内核的服务

什么是Shell脚本
> Shell 脚本（shell script），是一种为 shell 编写的脚本程序。和shell是不同的东西

## 第一个shell脚本

[first.sh]

```
#!/bin/sh   //必须要，而且要在第一行，用于告诉系统用哪种解释器
echo 'hello shell!'
```

* **`#!`** 是一种约定标记，用于告诉系统后面的脚本应该用什么解释器执行，即用哪种shell

  >linux支持多种shell
  >
  >- Bourne Shell（/usr/bin/sh或/bin/sh）
  >- Bourne Again Shell（/bin/bash）
  >- C Shell（/usr/bin/csh）
  >- K Shell（/usr/bin/ksh）
  >- Shell for Root（/sbin/sh）

* `echo` 命令用于将文本输出到窗口

* 执行脚本

  第一种方案：（这种方式脚本前的#!约定必须要有）
  
  ```
./first.sh //注意：前面的./不能省略。否则系统只会在PATH路径中查找first.sh，找不到后就提示不存在
  ```
第二种方案：（这种方式第一行约定是无效的）
  ```
  /bin/sh first.sh
  ```

* 执行时会提示 `permission denied`，这是因为当前用户对脚本没有执行权限，修改其权限

  ```
  chmod u+x .first.sh
  ```

## shell中的特殊字符

| 符号 | 作用                                                         |
| ---- | ------------------------------------------------------------ |
| ''   | 单引号。单引号中的所有字符包括特殊符号都没有特殊含义         |
| ""   | 双引号，在双引号中的特殊字符都没有特殊含义，但是"$"、"\"以及"`"是例外。 |
| ``   | 反引号，调用系统命令                                         |
| $()  | 和反引号一样，用来调用系统命令                               |
| #    | 注释                                                         |
| $    | 调用变量的值。一般配合{}使用                                 |
| \    | 转义符，跟在\后的特殊字符都将失去特殊意义                    |



## 变量

###定义变量时，变量名不需要美元符($)，如：

> login_name='admin'

变量名有一下约束：

 - 命名只能使用英文字母，数字和下划线，首个字符不能以数字开头。
 - 中间不能有空格，可以使用下划线（_）。
 - 不能使用标点符号。
 - 不能使用bash里的关键字（可用help命令查看保留关键字）。

### 变量的赋值：

方式一：

> login_name='admin' // 变量和=间不能有空格！！！！！

方式二：

> for login_name in 'admin'

### 使用变量：

使用变量需要加美元符($)，大括号时可选的

* $login_name
* ${login_name}

大括号用于分别变量名的边界，例如：

```
skill='php'
echo "good at ${skill}Script"
```

### 查看变量

```
set //这个命令可以查看自己定义的变量和所有环境变量

```



### 只读变量

通过readonly设置变量是只读，后面任何修改都会报错

```
login_name='admin'
readonly login_name
login_name='root' //这里会提示错误
```

### 删除变量

命令：unset

```
unset login_name
```

注意：删除后的变量不能再被使用
          不能删除只读变量。

### 变量类型

- **1) 局部变量** 局部变量在脚本或命令中定义，仅在当前shell实例中有效，其他shell启动的程序不能访问局部变量。
- **2) 环境变量** 所有的程序，包括shell启动的程序，都能访问环境变量，有些程序需要环境变量来保证其正常运行。必要的时候shell脚本也可以定义环境变量。
- **3) shell变量** shell变量是由shell程序设置的特殊变量。shell变量中有一部分是环境变量，有一部分是局部变量，这些变量保证了shell的正常运行

### declare命令

declare命令可以指定变量的类型
格式：
```
declare [+/-][rxi][变量名称＝设置值] 或 declare -f
选项：
  +/- 　"-"可用来指定变量的属性，"+"则是取消变量所设的属性。
  -f 　仅显示函数。
  r 　将变量设置为只读。
  x 　指定的变量会成为环境变量，可供shell以外的程序来使用。
  i 　[设置值]可以是数值，字符串或运算式。
```
例如：
默认情况下，linux将输入都当作字符串，所以在直接执行运算时只是普通的字符拼接；例如：

```
> aa=11
> bb=22
> cc=$aa + $bb
> echo $cc
$aa + $bb   //直接输出拼接后的字符串
> declare -i cc=$aa + $bb
>echo $cc
33  //cc是数值类型，所以自动算术运算
```

### 变量测试和替换

![img](https://images2015.cnblogs.com/blog/598639/201508/598639-20150831033513341-810765292.png)

## shell字符串

shell脚本中字符串的表示有单引号和双引号两种方式

* 单引号

  > greet='shell'
  >
  > str=`'hello \\"${greet}\\"'` //str实际值`hello \\"${greet}\\"`

  单引号字符限制

  * 单引号内的任何字符都会原样输出，不能使用变量
  * 单引号内不能有单引号，转义后的也不行。成对可以，表示拼接

* 双引号：可以出现变量和转义字符

  > greet='shell'
  >
  > str="hello \\"${greet}\\"" //str实际值hello shell
### 字符长度
  > str='1234'
  > echo ${#str} //输出4
### 截取字符串
	> str='hello script'
	> echo ${str:1:3} //输出ello
### 查找字符串
	> str='hello shell'
	> echo `expr index "$string" oe` //输出2,字符o和e那个先被找到先输出
	> 

## 数组
bash支持一维数组，数组下标从0开始，不固定长度
* 定义数组，使用圆括号表示，空格隔开元素
	> array_name=(值0 值1 ... 值n)
	> 也可以单独指定元素值
	> array_name[0]=值
	> array_name[2]=值
	> array_name[n]=值 
	> 这样定义的数组下标可以是不连续的，而且下表没有范围限制
* 读取数组 ${数组[n]},n是下标
  > ${array_name[0]}
  
  还可以用`@`代替下标，表示数组中所有的元素
  
  > echo ${array_name[@]}
* 获取数组长度
  > \$\{#array[@]}
  > ${#array[*]}

## shell 注释
脚本中以#开头的行都会被解释器忽略
```
##### 用户配置区 开始 #####
#
# 这里可以添加脚本描述信息
# 
##### 用户配置区 结束  #####
```

> 如果在开发过程中，遇到大段的代码需要临时注释起来，过一会儿又取消注释，怎么办呢？
> 每一行加个#符号太费力了，可以把这一段要注释的代码用一对花括号括起来，定义成一个函数，没有地方调用这个函数，这块代码就不会执行，达到了和注释一样的效果

多行注释：

```
:<<EOF
注释内容...
注释内容...
注释内容...
EOF
```

EOF可以用其它符号代替，例如

```
:<<'
注释内容...
注释内容...
注释内容...
'

:<<!
注释内容...
注释内容...
注释内容...
!
```



## shell 传递参数

shell脚本在执行时，可以接受参数；在脚本内通过`$n`的方式获取传递的参数

	* n=0:$0表示脚本名称
	* n>0:表示传递的第n个参数

```shell
#!/bin/sh

echo "shell: $0"
echo "第一个参数:$1"
echo "第二个参数:$2"
echo "第三个参数:$3"
```

运行结果

```shell
[admin@localhost shellscrip]$ ./tranpara.sh 1 2 3
shell: ./tranpara.sh
第一个参数:1
第二个参数:2
第三个参数:3
```

特殊的字符处理参数：

> * $# : 传递参数数量
> * $* :以一个字符串的形式显示所有向脚本传递的参数。就是将参数拼成了一个整体字符串
> * $$ :脚本当前的进程ID
> * $! :后台运行的最后一个进程的ID
> * $@ :传递的所有参数，类似一个数组存储所有的参数(但是不是数组)，参数间独立的。
> * $- : 显示Shell使用的当前选项，与set命令功能相同
> * $? :显示最后命令的退出状态。0表示没有错误，其他任何值表明有错误

## 运算符

### 算数运算符

* 方法一：使用expr或let
原生bash是不支持算数运算的，需要借助awk和expr命令来实现，常用expr。
expr是一个表达式计算工具

```
a=10
b=20

val=`expr $a + $b` //加法
//或 val =$(expr $a + $b) 注意+号两边的空白必须要有。下方的运算都可以用$()代替
echo "a + b : $val"

val=`expr $a - $b` //减法
echo "a - b : $val"

val=`expr $a \* $b` //乘法，注意要转义
echo "a * b : $val"

val=`expr $b / $a` //除法
echo "b / a : $val"

val=`expr $b % $a` //取模
echo "b % a : $val"

if [ $a == $b ] //相等
then
   echo "a 等于 b"
fi
if [ $a != $b ] //不等
then
   echo "a 不等于 b"
fi
```
* 方法二：使用$(())或$[]
例如：
```
a=10
b=20

$(($a + $b))或$[$a + $b]
```
ps：这里需要注意的是$()和$(())不一样。$()用于执行命令的

### 关系运算符

```
a=10
b=20

if [ $a -eq $b ] //相等判断
then
   echo "$a -eq $b : a 等于 b"
else
   echo "$a -eq $b: a 不等于 b"
fi
if [ $a -ne $b ] //不等判断，不相等返回true
then
   echo "$a -ne $b: a 不等于 b"
else
   echo "$a -ne $b : a 等于 b"
fi
if [ $a -gt $b ]  //大于判断，gt=great than
then
   echo "$a -gt $b: a 大于 b"
else
   echo "$a -gt $b: a 不大于 b"
fi
if [ $a -lt $b ] // 小于判断，lt = less than
then
   echo "$a -lt $b: a 小于 b"
else
   echo "$a -lt $b: a 不小于 b"
fi
if [ $a -ge $b ] // 大于等于判断，ge = great equal
then
   echo "$a -ge $b: a 大于或等于 b"
else
   echo "$a -ge $b: a 小于 b"
fi
if [ $a -le $b ] //小于等于判断。le = less equal
then
   echo "$a -le $b: a 小于或等于 b"
else
   echo "$a -le $b: a 大于 b"
fi
```

### 布尔运算

```
a=10
b=20

[![ $a == $b ]] //!：取反运算
[ $a -lt 100 -a $b -gt 15 ] //-a  类似&，与运算
[ $a -lt 100 -o $b -gt 100 ] // -o 类似|，或运算

```

### 逻辑运算符

```
&& : 逻辑与
|| ：逻辑或
```

### 字符串运算符

```
== :两个字符是否相等
!= :两个字符是否不等
-z ：字符长度是否为0，为0返回true
-n ：字符长度不为0，不为0返回true
$  :字符串是否为空字符串，不为空返回true
```

### 文件运算符

```
-b file :文件是否是块文件
-c file :文件是否是字符设备文件
-d file :文件是否存在且是否是目录文件 （常用）
-f file :文件是否存在且普通的文件（常用）
-g file ：检测文件是否设置了 SGID 位，如果是，则返回 true
-k file : 检测文件是否设置了粘着位(Sticky Bit)，如果是，则返回 true
-p file :检测文件是否是有名管道，如果是，则返回 true
-u file :检测文件是否设置了 SUID 位，如果是，则返回 true
-r file :文件是否存在且是否可读（常用）
-w file :文件是否存在且是否可写（常用）
-x file :文件是否存在且是否可执行（常用）
-s file ：文件是否为空（文件大小是否大于0）
-e file :文件目录是否存在（常用）
```

***条件中符号[ ] 和 [[ ]] 和(( ))和test 和 let之间的区别是啥？？？？？头大***

## 流程控制

### 条件控制

形式1:

```shell
if [ $a -gt 10 ]
then
	#内容
fi
```

需要注意：

	1. if和[之间必须存在空格
 	2. 同样[ ]之间必须要有间隔
 	3. then和fi不能省略

形式2:

```shell
if [ $a -gt 10 ]
then
	#内容
elsif [ $a -lt 5 ]
then
	#内容
else
	#内容
fi
```

### 循环结构

循环控制和高级语言中的作用是一样的。在循环中可以使用：

1. break跳出循环
2. continue 执行下一次循环，contiue后面的命令都会被本次循环忽略

形式1：

```shell
for variable in item1 item2 ... itemn
do
	#内容
done
```

形式2:

while循环如果条件condition为true一直循环。和下面的until停止条件相反

```shell
while condition
do
	#内容
done
```

例如：

```shell
#!/bin/bash
int=1
while(( $int<=5 ))
do
    echo $int
    let "int++"
done
```

例子中的let命令

> let 命令是 BASH 中用于计算的工具，用于执行一个或多个表达式，变量计算中不需要加上 $ 来表示变量。如果表达式中包含了空格或其他特殊字符，则必须引起来。
>
> 例如：
> 自加操作：**let no++**
> 自减操作：**let no--**
> 简写形式:  **let no+=10** 
> 普通运算:  **let no=10+2**

形式3:until

一直循环，直到condition为true

  ```
until condition
do
	# 内容
done
  ```



### CASE

Shell case语句为多选择语句。可以用case语句匹配一个值与一个模式，如果匹配成功，执行相匹配的命令

```
case 值 in
模式1) 
    command1
    command2
    ...
    commandN
    ;;   //类似break，如果匹配命令会从command1开始执行一直遇到；；
模式2）
    command1
    command2
    ...
    commandN
    ;;
*)       //类似default，如果值上面的值都不匹配，执行这个分支
		command1
		command2
		...
		commandN
		;;
esac    //表示结束的，必须要有，case 的反写
```



## shell函数

linux shell支持用户自定义函数，在需要的时候调用

```
[ function ] functionName [ () ]
{
	 #action;
	 [return int;]
}
```

1. 关键子function可以参略
2. 返回值return可以省略，如果省略将返回最后一条命令的结果。return 后跟的数值在（0-255）之间

例如：

```

funWithReturn(){
    echo "这个函数会对输入的两个数字进行相加运算..."
    echo "输入第一个数字: "
    read aNum  //用于用户输入并存储
    echo "输入第二个数字: "
    read anotherNum
    echo "两个数字分别为 $aNum 和 $anotherNum !"
    return $(($aNum+$anotherNum))
}
funWithReturn
echo "输入的两个数字之和为 $? !"
```

**注意**：所有函数在使用前必须定义。这意味着必须将函数放在脚本开始部分，直至shell解释器首次发现它时，才可以使用。调用函数仅使用其函数名即可。

## 文件包含

和其它语言一样，shell脚本也可包含其它shell脚本

语法：

```
. filename #注意点和filename中间有空格
或
source filename
```

> 注意：被包含的文件可以没有可执行权限