## HTML基础

1. 认识浏览器
   主流浏览器：chrome（谷歌，市场占有额最高）， IE（已经被微软放弃），Edge（和IE同属微软）, Firefox,  Opera, safari(属于苹果)

2. 浏览器内核

   * Trident(IE内核)
     使用的浏览器：IE，遨游， 世界之窗， avant， 腾讯TT， 猎豹安全浏览器，360极速浏览器，百度浏览器等
     Win10之后，微软将浏览器改为Edge浏览器，并且使用新的内核EdgeHTML。

   * Gecko（firefox）
     firefox的内核，特点是其代码开源

   * webkit
     Safari是苹果的浏览器，其内核就是webkit。

     还有使用webkit的浏览器：遨游浏览器3，Safari，Symbian手机浏览器，Android自带的浏览器

   * chromium/Blink(chrome)

     chromium项目中使用Blink内核，其实Blink是webkit的一个分支。

   * Presto（Opra）

     已经被Opera抛弃，Opera最新也采用了chrome的内核

3. web标准
   web标准的构成：

   * 结构标准：网页元素的整理和分类。分为xml和html两部分
   * 样式标准：主要是指对元素的样式设置标准。主要针对css
   * 行为标准：网页模型的定义和交互的编写，主要包括DOM和ECMAScript两个部分

4. HTML初识
   HTML：Hyper  Text Mark Language 超文本标记语言

5. HTML分类：

   * 双标签(html, head ,body...)
   * 单标签（br, hr, img...）

6. HTML关系

   * 嵌套关系
   * 兄弟关系

7. `<!DOCTYPE html>:告诉浏览器这是h5标准`

   

8. 字符集
   `<meta charset="utf-8">`:指定字符集

9. html标签语义话
   就是每个标签都具有其不同的意义，如h系列标签都表示标题类

   有点：

   * 方便代码阅读和理解
   * 更好的让浏览器和网络爬虫解析，从中分析内容（seo优化方便）
   * 有利于搜索引擎的优化



## 认识标签

### 排版标签

* 标题标签：h1 , h2 ,h3, h4, h5, h6
* 段落标签：p, hr（水平线标签，单标签）
* 换行标签：br(break的缩写，单标签)
* div标签和span标签：不具有语义，网页布局用的重要盒子（div是块元素，span是行内元素）

### 文字格式化标签：

* b/strong:文字<strong>粗体</strong>标签
* i/em:文字<i>斜体</i>标签
* s/del：文字加<s>删除线</s>标签
* u/ins: 文字加<u>下划线</u>标签

### 图形标签

`<img src="图像url"/>`

img属性：

* src：图片url位置
* alt：文本，图片不能显示时显示的文字
* title：文本，鼠标悬停时显示的内容
* width：图像的宽度（宽度和高度只修改其中一个，另外一个会自动按比例缩放）
* height：图像的高度
* border：数字，图像的边框宽度

### 链接标签

`<a href="url"></a>`

属性：

* href：url地址/锚点(#id)/javascript：开头的行为
* target: 链接打开的方式。_self：本标签；_blank：新开窗口

BASE标签

作用域：head

`<base target="链接的打开方式"/>`

设置页面的默认打开方式（target属性值）

### 特殊字符

| 特殊字符 | 描述   | 字符代码 |
| -------- | ------ | -------- |
|          | 空格   | `&nbsp;` |
| <        | 小于号 | `&lt;`   |
| >        | 大于号 | `&gt;`   |
| &        | 和号   | `&amp;`  |
| “        | 引号   | `&quot;` |
| &sup2;   | 平方   | `&sup2;` |
| &sup3;   | 立方   | `&sup3;` |



### 列表标签

无序列表：` <ul><li></li>...</ul>`

有序列表：`<ul><ol></ol></ul>`

自定义列表：

```
<dl>
	<dt>名词1</dt>
	<dd>名词解释1<dd>
	<dd>名词解释2<dd>
	<dd>名词解释3<dd>
	...
	<dt>名词n</dt>
	<dd>名词解释n1<dd>
	<dd>名词解释n2<dd>
	<dd>名词解释n3<dd>
</dl>
```



###表格

```
<table>
<caption>表格标题</caption> <!--可以省略-->
<thead> <!-这个和tbody可以省略-->
	<tr>
		<th>表头</th> <!--表头-->
		...
		<th>表头</th>
	</tr>
</thead>
</tbody>
	<tr>
		<td>内容</td> <!--单元格-->
		...
		<td>内容</td>
	</tr>
	...
	<tr>
		<td>内容</td>
		...
		<td>内容</td>
	</tr>
</thead>
</table>

```

table属性：

* border:数值，边框宽度，默认1px
* cellspacing:单元格之间的间距，默认2px
* cellpadding:单元格边框和文字的距离，默认1px
* width:表格宽度
* height:表格高度
* align：表格对齐方式

合并单元格：利用td的属性colspan和rowspan属性



### 表单

收集和提交信息的标签

### 表单元素

| 属性      | 属性值   | 描述               |
| --------- | -------- | ------------------ |
| type      | text     | 文本框             |
|           | password | 密码框             |
|           | radio    | 单选框             |
|           | checkbox | 复选框             |
|           | button   | 按钮               |
|           | submit   | 提交按钮           |
|           | reset    | 重置按钮           |
|           | image    | 图像形式的提交按钮 |
|           | file     | 文件域             |
| name      |          | 控件名称           |
| value     |          | 控件值             |
| size      | 整数     | 宽度               |
| checked   |          | 选择控件默认值     |
| maxlength | 整数     | 输入框最大字符数   |

### label标签：

label为input提供描述，点击文字能够定位到input控件。如下例中点击文字“输入账号”也可以自动聚焦到文本框

`<label>输入账号：<input type="text" /></label>`

label中多个表单元素时，可通过for属性定位到指定表单.下例中，点击文字会自动聚焦第二个输入框

`<label for="two">输入账号：<input type="text" /><input type="text" id="two" /></label>`

### 文本域标签 `<textarea></textarea>`

### 选择标签

```
<select>
  <option value="值" selected>文本（显示的内容）</option>
  ...
  <option value="值">文本（显示的内容）</option>
</select>
ps:selected设置默认选择的项
```



### html5新增标签

* header :定义文档的页眉部分

* nav：定义导航链接的部分

* footer：定义文档或节的页脚底部

* article：定义文章

* section：文档中的节

* aside：定义其所处内容之外的内容，侧边

* datalist：

  为输入框提供智能提示，提示内容由已经输入的值和datalist中的值匹配。

  ```
  <input type="text" list="mylist" />
  <datalist id="mylist">
  	<option>选项1</option>
  	<option>选项2</option>
  	<option>选项3</option>
  	...
  	<option>选项n</option>
  </datalist>
  ```

* fieldset: 组合框。

  ```
  <fieldset>
    <legend>描述</legend>
    其它html内容
  </fieldset>
  ```

### html5新增表单元素

| 类型     | 描述                 |
| -------- | -------------------- |
| email    | 邮箱                 |
| tel      | 手机号码             |
| url      | url格式              |
| number   | 数字格式             |
| search   | 搜索框，用于体现语义 |
| range    | 自由拖动的滑块       |
| time     | 小时分钟             |
| date     | 日期                 |
| datetime | 时间                 |
| month    | 年月                 |
| week     | xx年的第多少周       |
| color    | 取色板               |

例如邮箱：`<input type="email" />`

### html5新增的表单属性

* placeholder：文本，占位符
* autofocus：不需要值，页面加载后，自动聚焦到这个输入框
* multiple：不需要值，一次性上传多个文件
* autocomplete：on|off, 是否根据已经输入的值自动完成。条件，1.需要提交按钮，已经提交过。2.必须指定那么属性值
* required：不需要值，必填项
* accesskey: 字符， 设置组合快捷键。alt+设定的字符



### 多媒体标签

* embed:标签定义嵌入内容

* audio:播放音频

  `<audio src="../a.mp3" />`

  属性：

  autoplay：自动播放

  controls：显示控制器

  loop：数字，正整数循环指定次数，-1无限循环

  注意：不同浏览器支持的音频格式不一样，为了浏览器兼容，需要三种格式音频（ogg，mp3, wav）的存在。

  ```
  <audio>
  <source src="../a.ogg">
  <source src="../a.mp3">
  你的浏览器不支持音频播放
  </audio>
  ```

  

* video：播放视频

  属性：

  autoplay：自动播放

  controls：是否显示控制器

  loop：循环播放

  width：设置播放器宽度

  height：设置播放器高度

  

  注意：不同浏览器支持不同的视频格式，需要支持（ogg，mp4，webm ）三种格式。

  ```
  <video>
  <source src="../a.ogg">
  <source src="../a.mpr">
  你的浏览器不支持视频播放
  </video>
  ```

  