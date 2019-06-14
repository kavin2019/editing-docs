# Markdown从懵逼到入门

***

### 导语

> `Markdown`是一个轻量级的`「标记语言」`；其宗旨是`简单`，`易读`，其简化了文档的编写，摈弃了`Word`和`pages`上复杂的样式排版，Markdow最大的灵感来自于`纯文本Email`格式。其`轻量级`是相较于同为`标记语言`的HTML而言，HTML的标记符大约有上百之多，而Markdown只有十多种，可谓相当轻量

## 一 认识Markdown

 **Markdown**是一种[轻量级标记语言](https://zh.wikipedia.org/wiki/轻量级标记语言)，创始人为[约翰·格鲁伯](https://zh.wikipedia.org/wiki/約翰·格魯伯)（英语：John Gruber）；其语法目标是`成为一种适于网络的书写语言`,其目标不是替代HTML，而是让文档更容易编写 修改和阅读；它让用户更关注于文字内容，用`标记`来代替常用的排版，使得整个文章内容都能通过键盘就可完成。Markdown支持多种导出格式，例如html格式及pdf，当前很多网站使用Markdown来撰写帮助文档或博客笔记 个人在线简历等。

> 什么是markdown
> (1) a plain text formatting syntax; 
> (2) a software tool, written in Perl, that converts the plain text formatting to HTML

## 二 Markdown官方文档

* [英文官网文档](<https://daringfireball.net/projects/markdown/syntax>)

* [官方中文文档](http://wow.kuapp.com/markdown/)

## 三 Markdown语法

### 标题

markdown语法支持六级标题，标题文字前的符号'#'数量对应标题的级别。

例如：

```
# 一级标题
## 二级标题
### 三级标题
#### 四级标题
##### 五级标题
###### 六级标题
```

对应效果

> # 一级标题
>
> ## 二级标题
>
> ### 三级标题
>
> #### 四级标题

### 列表

无序列表前加符号`*`或者`+`或者`-`，例如：

```
* 语文
* 数学
* 英语
* 体育
```

其效果

> * 语文
> * 数学
> * 英语
> * 体育

有序列表则是通过在文字前加 '1.'，'2.'来实现，例如：

```
1. 规则
2. 原则
3. 法律
```

效果

> 1. 规则
> 2. 原则
> 3. 法律

有序列表前的数字并不是强制要求是连续或递增的，例如：

```
3. 规则
1. 法律
8. 原则
```

效果

> 3. 规则
> 1. 法律
> 8. 原则

> *PS: 无序列表和有序列表的符号和文字间必须存在一个或多个空格。*

### 区块引用

需要引用一段文字是可以通过符号大于号'>'引用的方式

```
> 风背着千山万水的鸣瑟，掀开秋日烟波，回旋的足迹，存留季节酝酿的意蕴华美，脚步化作轻微的涛声和窸窸窣窣的叶声，一任烟雨迷离，挥洒落幕的凄婉，只确定在这厚重的生命里随风潜入这段年华倾心的景色

>岱宗夫如何，齐鲁青未了。
>造化钟神秀，阴阳割昏晓。
>荡胸生层云，决眦入归鸟，
>会当凌绝顶，一览众山小。
```

> 风背着千山万水的鸣瑟，掀开秋日烟波，回旋的足迹，存留季节酝酿的意蕴华美，脚步化作轻微的涛声和窸窸窣窣的叶声，一任烟雨迷离，挥洒落幕的凄婉，只确定在这厚重的生命里随风潜入这段年华倾心的景色

>岱宗夫如何，齐鲁青未了。
>造化钟神秀，阴阳割昏晓。
>荡胸生层云，决眦入归鸟，
>会当凌绝顶，一览众山小。



### 链接

Markdown支持两种链接方式：内联式和参考式 

#### 内联式

```
[google](https://www.google.com.hk/)
```

> [google](https://www.google.com.hk/)

#### 参考式

```
[google][1]
[google hk][]

[1]:https://www.google.com.hk/
[google hk]:https://www.google.com.hk/
```

>[google][1]
>[google hk][]
>
>[1]: https://www.google.com.hk/ "谷歌在线"
>[google hk]: https://www.google.com.hk/  "谷歌香港"

> 链接的地址可以用尖括号包裹。例如：\<https://www.google.com.hk/\>

###  图片

#### 内联式

```
![](<https://rescdn.qqmail.com/zh_CN/htmledition/images/tg-bird1fbc69.png> "email")
```

效果

![](https://rescdn.qqmail.com/zh_CN/htmledition/images/tg-bird1fbc69.png "email")

#### 参考式
```
![][email]
![email1][]

[email]: https://rescdn.qqmail.com/zh_CN/htmledition/images/tg-bird1fbc69.png "来自email"
[email1]: https://rescdn.qqmail.com/zh_CN/htmledition/images/tg-bird1fbc69.png "来自email"
```
效果
![][email]
![email1][]

[email]: https://rescdn.qqmail.com/zh_CN/htmledition/images/tg-bird1fbc69.png "来自email"
[email1]: https://rescdn.qqmail.com/zh_CN/htmledition/images/tg-bird1fbc69.png "来自email"

### 强调

#### 加粗

加粗使用符号'**'或者'__'包裹文字

```
这段文字会**被加粗**
这段文字会__被加粗__
```

>这段文字会**被加粗**
>这段文字会__被加粗__

#### 斜体

斜体是使用单个'*'或'_'包裹文字

```
文字有一段是*斜体的*
文字有一段是_斜体的_
```

>文字有一段是*斜体的*
>文字有一段是_斜体的_

#### 删除线
文字加删除线通过使用双波浪线'\~~'包裹文字来实现的
```
这段文字的意义~~在于长时间的~~有效的
```
效果
> 这段文字的意义~~在于长时间的~~有效的



### 分割线

使用三个或三个以上的星号(*)  减号(-)或下滑线来建立一个分割符，行内不能有其它文字或字符

```
***
---
_____________________
```

效果

___



### 代码块

#### 内行码

使用两个反单引号来包裹

```
这是调用一个函数`calc(1.00,2.00)`
```

> 这是调用一个函数`calc(1.00,2.00)`

#### 代码块

使用三个连续的反单引号"```"来包裹

```
​```
function calc(double a,double b){
	return a+b;
}
​```
```

> ```
> function calc(double a,double b){
> 	return a+b;
> }
> ```

### 表格

表格是markdown中最复杂的一个功能
```
| 名称 | 地址 | 联系方式 |
| ---- | ---- | -------- |
|  国际商易大厦    | 宇宙     |    一年创空      |
|  本地居民楼    | 地球    |    一年创空      |
|  宇宙通讯协助调度统一管理控制中心协会    | 遥远的坦坦星球对面的哈哈星系     |    一年创空      |
```
效果
| 名称 | 地址 | 联系方式 |
| ---- | ---- | -------- |
|  国际商易大厦    | 宇宙     |    一年创空      |
|  本地居民楼    | 地球    |    一年创空      |
|  宇宙通讯协助调度统一管理控制中心协会    | 遥远的坦坦星球对面的哈哈星系     |    一年创空      |

对齐方式
* 左对齐： :------
* 右对齐：-----:
* 居中对齐：:----:
```
| 名称 | 地址 | 联系方式 |
| :---- | :----: | --------: |
|  国际商易大厦    | 宇宙     |    一年创空      |
|  本地居民楼    | 地球    |    一年创空      |
|  宇宙通讯协助调度统一管理控制中心协会    | 遥远的坦坦星球对面的哈哈星系     |    一年创空      |
```
效果
| 名称 | 地址 | 联系方式 |
| :---- | :----: | --------: |
|  国际商易大厦    | 宇宙     |    一年创空      |
|  本地居民楼    | 地球    |    一年创空      |
|  宇宙通讯协助调度统一管理控制中心协会    | 遥远的坦坦星球对面的哈哈星系     |    一年创空      |

### 兼容HTML

在markdown中可以插入html标签，markdown也是可以解析的；但是在html区块中的markdown语法是不能被解析的
```
这是一个普通的段落
<table>
	<tr>
		<td> *这段markdown内容是不能被解析的，所以左右星号不能起到斜体作用* <td>
	</tr>
</table>
```
这是一个普通的段落
<table>
	<tr>
		<td> *这段markdown内容是不能被解析的，所以左右星号不能起到斜体作用* <td>
	</tr>
</table>
> 所以很多效果如果markdown没有提供支持，可以使用html来实现。如文字需要下划线：\<u>文字</u>

### 其它

#### 反斜杠

反斜杠'\'可以用来进行转义，就是将markdown中具有特殊意义的字符和符号例如'*' '_'以及'>'等符号转化成一般符号。

例如：

```
\*本来是斜体的\*
```

效果

> \*本来是斜体的\*


####任务列表
```
- [x] 任务列表1
- [ ] 任务列表2
```
- [x] 任务列表
- [ ] 任务列表2

#### 公式块

##### 内联式

类似内联代码块，使用单个$符号包裹。例如：\$x^4\$

效果： $x^4$

##### 公式块

首位两行使用"$$"符号即可构建一个公式块

```
$$
√(x^m)
$$
```
效果
$$
√(x^m)
$$



## 四 markdown工具

### windows平台

* markdownPad
* MarkPad

### Mac平台

* Mou
* Typra

### Linux平台

* ReText

### 在线工具

* [简书](<https://www.jianshu.com/>)
* [drafit](https://draftin.com/)

#### 开发工具

* vscode + markdown preview enhanced

## 五 markdown的优势

1. 轻量级，易读易写
2. 兼容html，可以拓展
3. 可以发布成html格式或导出pdf
4. 直观，学习成本低

## 六 其它拓展
许多markdown工具和开发插件都支持对markdown的拓展
### TOC
全名title of content,内容的标题提取
例如 typora使用 `[TOC]`