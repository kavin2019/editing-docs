## 循环结构

***

### 第一种方式

```shell
for i in arg1 arg2 arg3 [arg...]
do
	#循环体
done
```

### 范例1

批量解压缩包

```shell
#!/bin/bash
#批量解压缩包
ls -*.tar.gz > ls.log
for i in $(cat log.txt)
do
   tar -zxf $i &>/dev/null;
done
rm -rf ls.log
```



### 第二种格式

常用语言中for语言格式

```shell
for (( 初始化变量;循环控制条件; 变量变化))
do
	#循环体
done
```

> 拓展：
>
> 双小括号运算符，**双圆括号结构是在bash中使用C语言风格变量操作的一种处理机制**，具有一下特征：
>
> 1. 所有表达式可以像c语言，如：a++,b-=1等，
> 2. 数字被当作数字类型，不再是默认字符类型
> 3. 所有变量可以不加$
> 4. 可以进行逻辑运算和四则运算，如：1+3,1>0等
> 5. 支持多个表达式，通过逗号隔开
> 6. 双括号结构 扩展了for，while,if条件测试运算

例如：

```shell
#!/bin/bash                                                                        
#计算1到100之和                                                                    
                                                                                   
s=0                                                                                
for((i=0;i<=100;i++))                                                              
do                                                                                 
   ((s=s+i));
   #或者 s=$(($s+$i)) 双小括号中的变量可以取消$符号。
   #又或者 s=`expr $s + $i` 这种方式注意+号和左右都需要空格，否则当字符串连接。这种方式等价 $($s + $i)
done                                                                               
                                                                                   
echo $s 
```



### 第三种格式

```
while[ 条件判断式 ]
do
	#	循环体
done
```

例如:

```shell
#!/bin/bash                                                                        
                                                                                   
i=0                                                                                
while [ $i -lt 100 ]                                                               
do                                                                                 
    s=$(( s+i))                                                                    
    ((i++))                                                                        
done                                                                               
                                                                                   
echo $s 
```

### 第三种格式

```shell
#until后面的空格一定要有,这个是如条件判断式不成立就执行循环，如果成立就跳出循环和while相反
until [ 条件判断式 ] 
do
	#循环体
done
```

例如

```shell
#!/bin/bash                                                                        
                                                                                   
i=0                                                                                
until [ $i -ge 100 ]                                                               
do                                

    s=$(( s+=i ))                                                                  
    i=$(( i+=1 ))                                                                  
done                                                                               
                                                                                   
echo $s;   
```

