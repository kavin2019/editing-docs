## JQuery

### 简介

JQuery是javascript的一个轻量级库，提供丰富的DOM操作和辅助函数。

### JQuery中的符号`$`

* 含义：JQuery对象的别名
* 作用：
  1. 选择并且包裹成特殊的对象（JQuery对象）；例如：`$('p a')`
  2. 作为命名空间，提供丰富的全局工具； 例如：`$.trim()`、`$.each`
  3. 根据html字符串创建DOM元素；例如：`$('<p>段落</p>').insertAfter('#id')`
  4. 建立代码，当DOM为操作准备好时立刻执行;例如：`$(function(){console.log('ready');})`等价于`$(document).ready(function(){console.log('ready');})`,浏览器解析DOM完成后立即调用functiion()函数；这有区别于window.onload=function(){}，onload需要页面全部解析并加载完成后才执行，如果页面中有大的图片时，延时会特别明显。
* 与其它js库符号`$`冲突时，使用`JQuery.noConfict()`。那么符号`$`非JQuery中的含义了。



### 选择并包裹DOM元素

#### 选择器

* CSS选择器
    1. 标签选择器；例如：div p document等
    
    2. id选择器；例如：#indentify,匹配id为indentify的所有DOM元素
    
    3. 类选择器；例如：.red，匹配class中包含`red`的DOM元素
    
    4. 混合选择器；例如： 
       a.red，匹配a标签中css类含有red的元素
       a.red.green,匹配同时包含red和green的css类的a标签
       a#indentify, 匹配id为indentify的a标签元素
    
    5. 后代选择器；例如：$('p a.red'), 匹配p标签后代中所有css类含有red的a标签元素
    
    6. 子选择器；例如：$('p > li > a'),匹配p标签的儿子标签中所有的的li，然后再选择li标签元素的儿子标签中的所有的a元素。这个不同与后代选择，后代中的选择器中的后代标签和祖先标签可以相隔多个层级。
       例如：
    
       ```html
       <ul class="mylist">
         <li>
           <a>hello</a>
         </li>
         <li>
           <p>
             <a>world</a>
           </p>
         </li>
       </ul>
       ```
    
       使用后代选择器：$('ul.mylist li a')，会选中节点中的所有a元素
    
       使用子选择器：$('ul.mlist > li > a'), 只会选中内容hello的a元素
    
    7. 属性选择器；故名思义，利用元素的属性名或者值来进行筛选。
    
       * 是否具有属性：$('a[href]'),选择所有包含href属性的a标签元素（无论href是否有值，值为甚么）。
       * 属性值以指定字符开头：$('a[href^=http]'),筛选所有href以http开头的a元素；例如`<a href="http://www.baidu.com">`是匹配的，`<a href="file:///c://a.txt">`时不匹配的。
       * 属性值完全相等：$('[href=a.txt]')
       * 属性值以指定字符串结尾的：`$('[href $= txt]')`
       * 属性值中包含指定字符串的：` $('[href *= jquery]')`
    
    8. 包含选择器：$('p:has(a)'),匹配包含a元素的所有p，a是p的后代（不需要是子标签）。不同于`$('p a')`,前者目的是匹配p，后者是匹配a。

汇总表：

| 选择器   | 描述                                                         |
| -------- | ------------------------------------------------------------ |
| *        | 匹配任何元素                                                 |
| E        | 匹配标签为E的所有元素                                        |
| E     F  | 匹配标签名为F，作为E标签后代节点的所有元素                   |
| E>F      | 匹配标签名为F，作为E的直接子节点的所有元素                   |
| E+F      | 匹配标签名为F，E是其相邻的下一个兄弟节点的所有元素           |
| E~F      | 匹配标签名为F，E是其之前的兄弟节点的所有元素（E和F可以不相邻） |
| E:has(F) | 匹配标签为E，至少有一个标签为F的后代节点的所有元素           |
| E.C      | 匹配带有类名C的所有元素E。.C等价于*.C                        |
| E#I      | 匹配id特性为I的所有元素E。#I等价于*#I                        |
| E[A]     | 匹配带有属性A的所有元素E。[A]等价于*[A]                      |
| E[A=V]   | 匹配带有属性A，并且A的值等于V的所有元素E                     |
| E[A^=V]  | 匹配带有属性A，并且A的值以V开头的所有元素E                   |
| E[A$=V]  | 匹配带有属性A，并且A的值以V结尾的所有元素E                   |
| E[A*=V]  | 匹配带有属性A，并且A的值包含V的所有元素E                     |



* 位置选择器

  | 选择器                | 描述                                                         |
  | --------------------- | ------------------------------------------------------------ |
  | :first                | 匹配中的第一个。例如：`$('a:first')`，表示页面中所有a元素中第一个 |
  | :last                 | 匹配中的最后一个。例如：`$('a:last')`，表示页面中所有a元素中最后一个 |
  | :even\|odd            | 匹配结果中的偶数或奇数位置                                   |
  | :eq(n)                | 匹配结果中索引位置为n的元素，n从0开始                        |
  | :gt(n)                | greater than 匹配结果中索引位置大于n的                       |
  | :lt(n)                | less than 匹配结果中索引位置小于n的                          |
  | :first-child          | 作为父节点的第一个子节点的元素。例如：$('li:firt-child')，作为第一个子元素的所有li元素 |
  | :last-child           | 作为父节点的最末一个子节点的元素。例如：$('li:last-child')，作为最末一个子元素的所有li元素 |
  | :only-child           | 没有兄弟节点的所有元素                                       |
  | :nth-child(n)         | 作为父节点下的第n个子节点。n从1开始                          |
  | :nth-child(even\|odd) | 作为偶数（even）或奇数（odd）的所有子节点                    |
  | :nth-child(nX+Y)      | X,Y为常量，n从0开始计算出所有符合要求位置的子节点。例如:$('li:nth-child(3n+1)'), |

  例如：

  ```html
  <ul>
    <li>1</li>
    <li>2</li>
    <li>3</li>
    <li>4</li>
    <li>5</li>
  </ul>
  <ul>
    <li>一</li>
    <li>二</li>
    <li>三</li>
    <li>四</li>
    <li>五</li>
  </ul>
  ```

  ```javascript
  $('li:first') //匹配 <li>1</li>
  $('li:last') //匹配 <li>五</li>
  $('li:eq(2)') //匹配 <li>3</li> ，注意索引的位置从0开始的
  $('li:gt(4)') //匹配第二ul下的所有li
  $('li:gt(5)') //匹配第一个ul下的所有li
  
  $('li:first-child') //匹配的是<li>1</li> 和<li>一</li>，因为它们是作为它们父级节点的第一个子元素
  $('li:last-child') //匹配的是<li>5</li> 和<li>五</li>，因为它们是作为它们父级节点的最后一个子元素
  $('li:nth-child(2)')//匹配的是<li>2</li> 和<li>二</li>，因为它们是作为它们父级节点的最后2个子元素
  ```

* 自定义筛选器

  | 筛选器         | 描述                                                         |
  | -------------- | ------------------------------------------------------------ |
  | :animated      | 选择当前处于动态控制之下的元素                               |
  | :button        | 选择所有的按钮，button、input[type=button]、input[type=submit]、input[type=reset] |
  | ：checkbox     | 选择复选框                                                   |
  | :checked       | 选择已经选中的复选框或单选框                                 |
  | :contains(str) | 选择包含文本str的元素                                        |
  | :disabled      | 选择已经禁用的表单元素                                       |
  | :enabled       | 选择在表单中已启用的元素                                     |
  | :file          | 选择文件元素，input[type=file]                               |
  | :header        | 选择标题元素，hn如：h1 h2 h3 … h6                            |
  | :hidden        | 选择隐藏的元素                                               |
  | :image         | 选择图像表单元素(input[type=image])                          |
  | :input         | 选择表单元素，input select textarea button                   |
  | :not(filter)   | 根据指定的筛选器反选                                         |
  | :parent        | 只选择拥有后代（包括文本）的元素                             |
  | :password      | 选择口令元素（input[type=passworld]）                        |
  | :radio         | 选择单选框（input[type=radio]）                              |
  | :reset         | 选择复位按钮（input[type=reset] 或 （button[type=reset]））  |
  | :selected      | 选择已经选中的元素                                           |
  | :submit        | 选择提交按钮（input[type=submit] 或 （button[type=submit]）） |
  | :text          | 选择文本字段元素(input[type=text])                           |
  | :visible       | 选择可见元素                                                 |

  



### 通过html格式字符串创建新的元素到DOM

* 完整html字符：`$('<div>hello</div>')`,
* 创建空元素：`$('div')`; 其它标签也可以，但是script标签有问题！

通过JQuery创建的实际是JQuery的一个对象，它包含了所有JQuery对象的所有方法。所以创建后可以通过操作DOM方法添加到文档树中。



### 管理包装集合（JQuery对象）

1. 包装集合大小

   * 通过属性length
   * size
     格式：size()
     返回值：int，元素个数

2. 从包集合获取元素

   * 方法1:通过下标的方式；如：$('a')[0], 获取出来的是DOM元素，不在是JQuery对象
   * 方法2:通过get方法
     格式：get(index)
     参数： 
          index: 元素在包集合中的索引，从0开始，小于包装集合大小。如果省略，以数组的形式返回元素。
     返回：
         DOM元素
     例如：$('a').get(0);

3. 获取元素在包装集合中的位置索引

   格式：index(element)

   参数：

    element：元素的引用，用于确定元素的下标

   返回：

     传入元素在包装集合中的顺序下标；如果找不到，返回-1；



### 筛选包装集合

1. 添加更多元素到集合

   * 方法一：

     通过选择器，如：$('img, a') ,通过逗号形成或的关系。

   * 方法二：

     通过方法add，add方法可以扩大包装集合

     格式： add(expression)

     参数：

     expression: 可以是选择器，html片段字符串，DOM元素或元素集合

     返回：

       包装集合

2. 移除指定元素

   * 方法一：在添加的时候，选择器使用not来进行过滤

     例如：`$(input[type]:not([type=text]))`；选择所有除文本输入框以外的所有表单元素

   * 方法二：通过包装集合的方法not

     格式：not(expression)

     说明：根据表达式的值，从包装集合中删除元素。如果参数是选择器，那么删除包装集合中所有匹配表达式的元素；如果参数是元素的引用，则从包装集合中删除该元素

     参数：

     ​    expression: （字符串|元素|数组）选择器，元素引用或元素引用数组。

     返回：

       包装集合

     例如： $('input[type]').not('[type=text]');

3. 包装集的筛选方法filter
   利用传入的选择表达式或筛选函数，从包装集里筛选元素

   格式：filter(expression)

   参数：

   expression: (字符串|函数) 如果参数是字符串，则指定jquery筛选器，从包装集中删除不匹配的元素。如果是一个函数，那么用于确定筛选条件，为包装集中的每个元素各调用一次改函数，以当前元素为上下文（this），返回值为false的任何元素都会从包装集中删除

   返回值：

     包装集

4. 获取包装集的子集

   slice(begin, end)

   创建并返回新的包装集，新包装集包含原始包装集的连续的一部分

   参数：

   begin  (数字) 开始位置，包含开始位置的元素

   end   (数字)解释位置，该位置的元素不被包含，如果缺省或大于包装集的大小，默认到包装集的末尾

   返回值：

   包装集

5. 通过关系获取子包装集

   | 方法               | 描述                                                         |
   | ------------------ | ------------------------------------------------------------ |
   | children(selector) | 获取所有原始包装集中元素下的所有唯一元素组成的新包装集；唯一：表示不相同的，下面的也是 |
   | contents()         | 获取原始包装集中元素的所有内容的新包装集                     |
   | next(selector)     | 返回原始包装集元素下一个相邻的唯一的兄弟节点元素所组成的新包装集合 |
   | nextAll(slector)   | 返回原始包装集中元素之后的所有兄弟节点组成的新包装集         |
   | parent(selector)   | 返回原始包装集中元素的父节点组成的新包装集                   |
   | parents(selector)  | 返回原始包装集中元素唯一祖先节点元素（包括父节点以及向上追溯到的所有节点，不包括document）组成的新包装集。 |
   | prev(selector)     | 返回原始包装集中元素相邻的上一个节点元素的新包装集           |
   | prevAll(selector)  | 返回原始包装集中元素之前的所有兄弟节点元素的新包装集         |
   | siblings(selector) | 返回原始包装集中元素的所有唯一兄弟元素组成的新包装集         |

   

6. find方法

   find(selector)

   以原始包装集为上下文，再进行筛选，将匹配的元素组成新的包装集。

   参数：

   selector:筛选器

   返回值：

   新的包装集

7. contains方法

   利用元素的文本内容匹配新的包装集

   contains(text)

   参数：

   text: 字符串，添加到新的包装集中的元素的文本都包含这个字符串

   返回值：

   新的包装集

8. 测试包装集

   is(selector)

   测试包装集中是否有元素匹配传入的选择器，如果有返回true，否则返回false

   参数：

   selector: 选择器

   返回值：

   布尔值（true或false)

### JQuery链

JQuery链为执行命令提供了强大的功能。

1. end方法

   在JQuery链中恢复上一个包装集。例如`$('a').find('[href^=http]').css('color','red').end().css('border','1px solid black')`,在链式中，find方法新建新的包装集，这样就不能访问之前的包装集了而是访问新的包装集，通过end方法可以恢复到原始的包装集。

### 操作包装集中的元素

1. 遍历包装集中的元素

   each(iterator)

   遍历包装集中的所有元素，为各元素各自调用传进来的iterator函数

   参数：

   iterator：函数，形式如：function(index){},index为元素在包装集中的位置，而当前元素可以通过this来访问

2. 操作元素的属性

   * 获取属性

     attr(name)

     name为属性的名称

   * 设置属性

     * attr(name, value)

       name:属性的名称

       value：属性的值，可以是字符串或对象或函数；如果是函数那么，attr会为每个元素调用一次这个函数，然后从0下标传入索引，函数体内用this引用当前元素，最后函数值设置为属性的值。

     * attr(attributes)

       attributes: 对象，同时设置多个属性的值，例如：{value: '', title:'hello'}

   * 删除属性

     removeAttr(name)

     name:属性名称

3. 操作元素的样式

   * 添加和删除类

     ```
     addClass(names)
     添加一个或多个类名称到包装集的元素中
     参数：
     names 字符串 一个类名或多个类名，多个类型间用空格隔开
     返回：
     包装集
     ```
     ```
     remvoeClass(names)
     将包装集中的元素移除一个或多个类型名
     参数：
     names 字符串 一个类名或多个类名，多个类型间用空格隔开
     返回值：
     包装集
     ```
     
     ```
     toggleClass(name)
     如果元素中不存在名为name的类，那么添加；如果存在就移除。注意：包装集中的元素是单独执行的，部分添加，部分可能是移除。
     参数：
     name 类名（单独的）
     返回值：
     包装集
     ```
     
     ```
     hasClass(name)
     确定包装集中是否有元素包含name的类，如果有任意一个元素包含，返回true。否则返回false
     参数：
     name 单个类名
     返回值：
     布尔值
     ```
     
     
     
   * 获取和设置样式属性

     ```
     css(name,value)
     机制和attr一样，为包装集中的每个元素设置指定的样式属性的值
     参数：
     name：样式属性名称；例如：color、border、background等
     value:  (字符串|数字|函数) 如果value为函数时，每个元素都会调用这个函数，然后以返回值作为样式属性的值，函数体内使用this引用当前元素。
     返回：
     包装集
     ```

     ```
     css(properites)
     为包装集中的每个元素设置多个样式属性
     参数：
     properites 对象，对象的属性和值会被复制赋给包装集中的元素。例如：{color:red,width:300px}
     返回值：
     包装集
     ```

     ```
     css(name)
     获取已经计算过的css样式属性的值
     参数：
     name 字符串 属性名
     返回值：
     字符串，已经算的样式值
     ```

     ```
     width(value)
     height(value)
     设置包装集中所有元素的高度和宽度
     参数：
     value 数字 以px为单位
     返回：
     包装集
     ```

     ```
     width
     height
     获取包装集中第一个元素的高度和宽度
     返回值：
     数字。以px为单位
     ```

     

4. 操作DOM

   * 插入DOM节点

     ```
     .append(value)
     在包装集中每个元素的子节点最后添加元素，如果value是已存在的DOM元素，那么如果包装集如果是单个则是移动操作，否则包装集中目标元素多个的话就是复制操作（原来的元素仍在原来的位置）
     参数：
     value  (字符串|元素|包装集对象)
     返回值：
     包装集
     ```

     ```
     .appendTo(target)
     把包装集中的元素移动到指定的目标节点内容的末尾
     参数：
     target (字符串|元素) 字符串为选择器，元素为一个DOM元素。如果目标中匹配有多个元素，那么包装集中的各元素被复制并追加到目标匹配的多个元素内容末尾
     返回值：
     包装集
     ```

     其它操作（参数和append以及appendTo一致，同样如果目标元素有多个则复制，否则就移动）

     | 方法                  | 描述                                      |
     | --------------------- | ----------------------------------------- |
     | .prepend(content)     | 添加元素到目标节点内容的开头              |
     | .prependTo(target)    | 将包装集中的元素添加到目标节点            |
     | .before(content)      | 将content添加在包装集内元素节点位置的前面 |
     | .insertBefore(target) | 将包装集内元素添加到目标节点位置之前      |
     | .after(content)       | 将content添加在包装集内元素节点位置的后面 |
     | .insertAfter(target)  | 将包装集内元素添加到目标节点位置之后      |

     

   * 包裹元素

     ```
     .wrap(wrapper)
     把包装集中的元素都使用html格式片段或元素的克隆副本分别包裹起来
     参数：
     wrapper (字符串|元素) 字符串是用于包裹元素的开始标签和结束标签（例如：'<div></div>'),或者一个将被克隆且用作包裹的元素
     返回值：
     包装集
     ```

     具有相同作用，不同效果的其它方法：

     | 方法                | 描述                                                         |
     | ------------------- | ------------------------------------------------------------ |
     | .wrapAll(wrapper)   | 把包装集中所有的元素作为一个单元包裹在wrapper中              |
     | .wrapInner(wrapper) | 将包装集中每个元素中的内容包裹起来。和wrap方法内似包裹outterHtml和innerHtml |

     

   * 删除元素

     ```
     .remove()
     从DOM中移除包装集中所有的元素；但是包装集中这个元素还是存在，可进一步是用其它JQuery命令操作。
     返回值：
     包装集
     ```

     ```
     .empty()
     清空包装集中各个元素内的内容
     返回值：
     包装集
     ```

     常见使用`.remove`来实现替换：

     ```
     $.fn.replaceWith = function(html){
         //先添加，然后在移除
     		return this.after(html).remove();
     }
     ```

   * 克隆元素

     ```
     .clone(copyHandlers)
     创建包装集里元素的副本，并返回包含这些副本的新包装集，元素以及元素的后代都会复制，事件处理程序是否复制取决于参数copyHandlers
     参数：
     copyHandlers (true|false) true为复制事件处理程序，false为不复制
     返回值：
     新的包装集
     ```

     

   * 获取表单元素的值

     ```
     .val()
     获取表单元素的值。如果是input、select以及textarea时返回输入或选择的值，如果是空包装集调用，返回undefined
     返回值：
     包装集
     ```

     ```
     .val(value)
     设置表单元素的值
     参数：
     value (字符串|数值|函数) 字符或数值直接设置表单元素的值。如果为函数，那么函数的形式`function(index,oldValue){}`，index表示元素在包装集中的位置索引，oldvalue表示原来的值；注意：可以通过直接修改oldvalue值来修改表单元素的值。例如：
     //表单元素失去焦点后，将表单内的值改为大写。
     $( "input" ).on( "blur", function() {
       $( this ).val(function( i, val ) {
         return val.toUpperCase();
       });
     });
     ```

     

   * 

5. 