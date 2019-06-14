## 条件判断

### 判断方式（两种）

* test OPTIONS filename
* [ OPTIONS filename ] 注意中括号间需要有空格，否则会报错

### 文件类型判断

> -d 文件是否存在，并且是否是目录
> -f 文件是否存在，并且是否是普通文件
> -e 文件是否存在

### 文件权限

> -r 文件是否存在，并且可读
> -w 文件是否存在，并且可写
> -x 文件是否存在，并且可执行

### 文件之间的判断

> file1 -nt  file2 文件1是否比文件2新（[n]ewer [t]han）
> file1 -ot  file2 文件1是否比文件2旧（[o]lder [t]han）
> file1 -et  file2 文件1是否和文件2INode一样([e]quel [f]ull)

### 两个数字键的判断

> 数字1 -eq 数字2 ： 数字1==数字2
> 数字1 -nq 数字2 ： 数字1 ！= 数字2
> 数字1 -gt 数字2 ： 数字1 > 数字2
> 数字1 -ge 数字2 ：数字1 >= 数字2
> 数字1 -lt 数字2 ：数字1 < 数字2
> 数字1 -le 数字2 ：数字1 <= 数字2

### 字符串判断

> -z string ：string是否为空
> -n string ： string是否不空
> str1 == str2 ： str1是否和str2相等
> str1 != str2 : str1是否不等于str2

### 多重判断

> 判断1 -a 判断2 ：逻辑与
> 判断1 -o 判断2 ：逻辑或
> ! 判断 ：逻辑非。感叹号和判断间有空格

### 初级范例

条件判断本身没有返回值。可以通过关系运算来实现输出

```
#没有输出结果，但是可以通过$?获取执行结果，如果存在为0，否则为1
[root@localhost ~]# test -e gt.js
[root@localhost ~]# echo $?
0
[root@localhost ~]# [ -e gt.js ]
[root@localhost ~]# echo $?
0
#可以通过关系运算来实现输出
[root@localhost ~]# [ -f gt.js ] && echo yes || echo no
yes
[root@localhost ~]# [ -d gt.js ] && echo yes || echo no
no
```

