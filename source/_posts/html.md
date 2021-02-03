---
title: html
toc: true
date: 2020-06-29 20:29:05
tags:
categories:
- 前端
---

## 前端概述 
<!--more-->
 

![img](image/877318-20160622221214969-916045696.png)

```
import socket

def main():
    sock = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
    sock.bind(('localhost',8089))
    sock.listen(5)

    while True:
        connection, address = sock.accept()
        buf = connection.recv(1024)

        connection.sendall(bytes("HTTP/1.1 201 OK\r\n\r\n","utf8"))

        connection.sendall(bytes("<h1>Hello,World</h1>","utf8"))

        connection.close()

if __name__ == '__main__':

    main()
```

### html css 以及js的关系

![img](image/877318-20160621155609897-1758730933.jpg)

## HTML的学习

### HTML 是什么？

htyper text markup language  即超文本标记语言

超文本: 就是指页面内可以包含图片、链接，甚至音乐、程序等非文字元素。

标记语言: 标记（标签）构成的语言.

 

网页==HTML文档，由浏览器解析，用来展示的

静态网页：静态的资源，如xxx.html

动态网页：html代码是由某种开发语言根据用户请求动态生成的

**html文档树形结构图：**

​                      ![img](image/877318-20161025132859984-662031019.png)

### 什么是标签

- 是由一对尖括号包裹的单词构成 例如: `<html>` *所有标签中的单词不可能以数字开头.
- 标签不区分大小写.`<html>` 和 `<HTML>`. 推荐使用小写.
- 标签分为两部分: 开始标签`<a>` 和 结束标签`</a>`. 两个标签之间的部分 我们叫做标签体.
- 有些标签功能比较简单.使用一个标签即可.这种标签叫做自闭和标签.例如: `<br/> <hr/> <input /> <img />`
- 标签可以嵌套.但是不能交叉嵌套. `<a><b></a></b>`

### 标签的属性

- 通常是以键值对形式出现的. 例如 name="alex"
- 属性只能出现在开始标签 或 自闭和标签中.
- 属性名字全部小写. *属性值必须使用双引号或单引号包裹 例如 name="alex"
- 如果属性值和属性名完全一样.直接写属性名即可. 例如 readonly

### <!DOCTYPE html>标签

​      由于历史的原因，各个浏览器在对页面的渲染上存在差异，甚至同一浏览器在不同版本中，对页面的渲染也不同。在
W3C标准出台以前，浏览器在对页面的渲染上没有统一规范，产生了差异(Quirks mode或者称为Compatibility 
Mode)；由于W3C标准的推出，浏览器渲染页面有了统一的标准(CSScompat或称为Strict mode也有叫做Standars
mode)，这就是二者最简单的区别。
​      W3C标准推出以后，浏览器都开始采纳新标准，但存在一个问题就是如何保证旧的网页还能继续浏览，在标准出来以前，
很多页面都是根据旧的渲染方法编写的，如果用的标准来渲染，将导致页面显示异常。为保持浏览器渲染的兼容性，使以
前的页面能够正常浏览，浏览器都保留了旧的渲染方法（如：微软的IE）。这样浏览器渲染上就产生了Quircks mode
和Standars mode，两种渲染方法共存在一个浏览器上。

```
window.top.document.compatMode：
//BackCompat：怪异模式，浏览器使用自己的怪异模式解析渲染页面。 
//CSS1Compat：标准模式，浏览器使用W3C的标准解析渲染页面。
```

​       这个属性会被浏览器识别并使用，但是如果你的页面没有DOCTYPE的声明，那么compatMode默认就是BackCompat,

这也就是恶魔的开始 -- 浏览器按照自己的方式解析渲染页面，那么，在不同的浏览器就会显示不同的样式。

​    如果你的页面添加了<!DOCTYPE html>那么，那么就等同于开启了标准模式，那么浏览器就得老老实实的按照W3C的

标准解析渲染页面，这样一来，你的页面在所有的浏览器里显示的就都是一个样子了。

这就是<!DOCTYPE html>的作用。

### head标签

<meta>

​         meta标签的组成：meta标签共有两个属性，它们分别是http-equiv属性和name 属性，不同的属性又有不同的参数值，这些不同的参数值就实现了不同的网页功能。

​        1: name属性主要用于描述网页，与之对应的属性值为content，content中的内容主要是便于搜索引擎机器人查找信息和分类信息用的。     

​        2: http-equiv顾名思义，相当于http的文件头作用，它可以向浏览器传回一些有用的信息，以帮助正确和精确地显示网页内容，与之对应的属性值为content，              content中的内容其实就是各个参数的变量值。   

 注意：X-UA-Compatible

```
每个主要版本IE新增的功能都是为了让浏览器更容易使用、增加安全性及更支持业界标准。以这些作为IE的特色，其中
一个风险就是旧版本网站无法正确的显示。

为了将这个风险降到最低，IE6允许网页开发人员选择IE编译和显示他们网页的方式。"Quirks mode"为预设，这会
使页面以旧版本浏览器的视点显示，"Standards mode"(也称为"strict mode")特点是支持业界标准最为完善。
然而要利用这个增强的支持功能，网页必须包含恰当的<!DOCTYPE>指令。

若一个网页没有包含<!DOCTYPE>指令，IE6会将它以quirks mode显示。若网页包含有效的<!DOCTYPE>指令但浏
览器无法辨识，IE6会将它以IE6 standards mode显示。因为少数网站已经包含<!DOCTYPE>指令，兼容性模式的
切换相当成功。这使网页开发人员能选择将他们的网页转移为standards mode的最佳时机。

随著时间经过，更多网站开始使用standards mode。它们也开始使用IE6的特性和功能来检测IE。举例来说，IE6
不支持universal selector(即css之全局选择器 * {})，一些网站便使用它来针对IE做特定的对应。

当 IE7增加了对全域选择器的支持，那些依赖IE6特点的网站便无法侦测出这个新版本的浏览器。因此那些针对IE的
特定对应无法应用于IE7，造成这些网站便无法如他们预期的显示。由于<!DOCTYPE>只支持两种兼容性模式，受到影
响的网站拥有者被迫更新他们的网站使其能支持IE7。

IE8 比之前的任何版本浏览器都更支持业界标准，因此针对旧版本浏览器设计的网页可能无法如预期般呈现。为了帮
助减轻所有问题，IE8引入文件兼容性的概念，使你能选择你的网页设计要对应的特定IE版本。文件兼容性在IE8增加
了一些新的模式，这些模式能告诉浏览器如何解析和编译一个网页。若你的网页无法在 ie8正确的显示，你可以更新
你的网站使它支持最新的网页标准(优先选项)或在你的页面上新增一个meta元素用于告诉IE8如何依照旧版本浏览器
编译你的页面。

这能让你选择将你的网站更新支持IE8新特点的时机。

当 Internet Explorer 8 遇到未包含 X-UA-Compatible 标头的网页时，它将使用 <!DOCTYPE> 指令来确
定如何显示该网页。 如果该指令丢失或未指定基于标准的文档类型，则 Internet Explorer 8 将以 IE5 模式
（Quirks 模式）显示该网页。
```

 **非meta标签**

```
    <title>oldboy</title>
    <link rel="icon" href="http://www.jd.com/favicon.ico">
    <link rel="stylesheet" href="css.css">
    <script src="hello.js"></script>　
```

### body标签

#### 一 基本标签（**块级标签和内联标签**）

```
<hn>: n的取值范围是1~6; 从大到小. 用来表示标题.

<p>: 段落标签. 包裹的内容被换行.并且也上下内容之间有一行空白.

<b> <strong>: 加粗标签.

<strike>: 为文字加上一条中线.

<em>: 文字变成斜体.

<sup>和<sub>: 上角标 和 下角表.

<br>:换行.

<hr>:水平线

<div><span>
```

**块级标签**：`<p><h1><table><ol><ul><form><div>`

**内联标签：**`<a><input><img><sub><sup><textarea><span>`

**block（块）元素的特点**

 总是在新行上开始；
 **宽度缺省是它的容器的100%，除非设定一个宽度。**
 它可以容纳内联元素和其他块元素

**inline元素的特点**

和其他元素都在一行上；
宽度就是它的文字或图片的宽度，不可改变
内联元素只能容纳文本或者其他内联元素

**特殊字符**

​      &lt; &gt；&quot；\&copy;\&reg;

**标签属性**

id 标签的属性！！！！！  用#符号来引用

#### 二 图形标签: \<img> 

```
src: 要显示图片的路径.

alt: 图片没有加载成功时的提示.

title: 鼠标悬浮时的提示信息.

width: 图片的宽

height:图片的高 (宽高两个属性只用一个会自动等比缩放.)
```

#### 三 超链接标签(锚标签)\<a>

```
href:要连接的资源路径 格式如下: href="http://www.baidu.com" 

target: _blank : 在新的窗口打开超链接. 框架名称: 在指定框架中打开连接内容.

name: 定义一个页面的书签.

用于跳转 href : #id.（锚）
 
```

#### 四 列表标签：

```
<ul>: 无序列表

<ol>: 有序列表
         <li>:列表中的每一项.

<dl>  定义列表

         <dt> 列表标题
         <dd> 列表项
```

#### 五 表格标签: \<table>

```
border: 表格边框.

cellpadding: 内边距

cellspacing: 外边距.

width: 像素 百分比.（最好通过css来设置长宽）

<tr>: table rowc

<th>: table head cell

<td>: table data cell

rowspan:  单元格竖跨多少行

colspan:  单元格横跨多少列（即合并单元格）

<th>: table header <tbody>(不常用): 为表格进行分区.
```

#### 六 表单标签\<form>

​      表单用于向服务器传输数据。

​      表单能够包含 [input 元素](http://www.w3school.com.cn/tags/tag_input.asp)，比如文本字段、复选框、单选框、提交按钮等等。

​      表单还可以包含[textarea](http://www.w3school.com.cn/tags/tag_textarea.asp)、select、[fieldset](http://www.w3school.com.cn/tags/tag_fieldset.asp)和 [label 元素](http://www.w3school.com.cn/tags/tag_label.asp)。

**1.表单属性**

　　HTML 表单用于接收不同类型的用户输入，用户提交表单时向服务器传输数据，从而实现用户与Web服务器的交互。表单标签, 要提交的所有内容都应该在该标签中.

​       action: 表单提交到哪. 一般指向服务器端一个程序,程序接收到表单提交过来的数据（即表单元素值）作相应处理，比如https://www.sogou.com/web

​       method: 表单的提交方式 post/get 默认取值 就是 get（信封）

​                          get: 1.提交的键值对.放在地址栏中url后面. 2.安全性相对较差. 3.对提交内容的长度有限制.4.请求或查看数据时使用。

​                          post:1.提交的键值对 不在地址栏. 2.安全性相对较高. 3.对提交内容的长度理论上无限制.

​                          get/post是常见的两种请求方式.

**2.表单元素**

​          **\<input>  标签的属性和对应值**              

```
type:        text 文本输入框

             password 密码输入框

             radio 单选框

             checkbox 多选框  

             submit 提交按钮（submit的value属性只是用于显示）     

             button 按钮(需要配合js使用.) button和submit的区别？

             file 提交文件：form表单需要加上属性enctype="multipart/form-data"  
             
             reset 重置：value为按钮上显示的字符
         
```

```
name: 表单提交项的键.注意和id属性的区别：name属性是和服务器通信时使用的名称；而id属性是浏览器端使用的名称，该属性主要是为了方便客户端编程，而在css和javascript中使用的
value:   表单提交项的值.对于不同的输入类型，value 属性的用法也不同
placeholder：放在输入框后
checked:  radio 和 checkbox 默认被选中
readonly: 只读. text 和 password
disabled: 对所用input都好使.
```

上传文件注意两点：

 1 请求方式必须是post

 2 enctype="multipart/form-data"

```
def index(request):
    print request.POST
    print request.GET
    print request.FILES
    for item in request.FILES:
        fileObj = request.FILES.get(item)
        f = open(fileObj.name, 'wb')
        iter_file = fileObj.chunks()
        for line in iter_file:
            f.write(line)
        f.close()
    return HttpResponse('ok')
```

 **\<select> 下拉选标签属性**

```
          name:表单提交项的键.

          size：选项个数

          multiple：multiple 

                 <option> 下拉选中的每一项 属性：

                       value:表单提交项的值.   selected: selected下拉选默认被选中

                 <optgroup>为每一项加上分组
                 				<optgroup label = "name">
```

   **\<textarea> 文本域**              

```
name:    表单提交项的键.

cols:    文本域默认有多少列

rows:    文本域默认有多少行
```

​    **\<label>**    获得对应id焦点

```html
<label for = "wwww"></lable>
<input id = "www" type="text"></input>
```

​    **\<fieldset>**

```html
<fieldset>
  <legend>
    登录吧
  </legend>
  <input type="text">
</fieldset>
```



## [CSS](https://www.cnblogs.com/yuanchenqi/articles/5977825.html)

### css概述

CSS是Cascading Style Sheets的简称，中文称为层叠样式表，用来控制网页数据的表现，可以使网页的表现与数据内容分离。

**基本语法：**

selector{

property:value

}

属性大于1个之后，属性之间用分号隔开

值大于1个单词，需要加上引号

### 一 css的四种引入方式

**1.行内式**
          行内式是在标记的style属性中设定CSS样式。这种方式没有体现出CSS的优势，不推荐使用。

```
<p style="background-color: rebeccapurple">hello yuan</p>
```

**2.嵌入式**
          嵌入式是将CSS样式集中写在网页的<head></head>标签对的<style></style>标签对中。格式如下：

```
<head>
    <meta charset="UTF-8">
    <title>Title</title>
    <style>
        p{
            background-color: #2b99ff;
        }
    </style>
</head>
```

**3 链接式**
            将一个.css文件引入到HTML文件中

```
<link href="mystyle.css" rel="stylesheet" type="text/css"/>
```

**4.导入式**
          将一个独立的.css文件引入HTML文件中，导入式使用CSS规则引入外部CSS文件，`<style>`标记也是写在`<head>`标记中，使用的语法如下：    

```
<style type="text/css">
 
          @import"mystyle.css"; 此处要注意.css文件的路径
 
</style>　
```

注意：

​      导入式会在整个网页装载完后再装载CSS文件，因此这就导致了一个问题，如果网页比较大则会儿出现先显示无样式的页面，闪烁一下之后，再出现网页的样式。这是导入式固有的一个缺陷。使用链接式时与导入式不同的是它会以网页文件主体装载前装载CSS文件，因此显示出来的网页从一开始就是带样式的效果的，它不会象导入式那样先显示无样式的网页，然后再显示有样式的网页，这是链接式的优点。

### 二 css的选择器（Selector）

“选择器”指明了{}中的“样式”的作用对象，也就是“样式”作用于网页中的哪些元素

#### 1 基础选择器

```
＊ ：通用元素选择器，匹配任何元素                    * { margin:0; padding:0; }

E ：标签选择器，匹配所有使用E标签的元素               p { color:green; }

.info和E.info: class选择器，匹配所有class属性中包含info的元素   .info { background:#ff0; }    p.info { background:blue; }

#info和E#info  id选择器，匹配所有id属性等于footer的元素         #info { background:#ff0; }   p#info { background:#ff0; }
```

#### 2 组合选择器

```
 E,F  多元素选择器，同时匹配所有E元素或F元素，E和F之间用逗号分隔         div,p { color:#f00; }
 E F  后代元素选择器，匹配所有属于E元素后代的F元素，E和F之间用空格分隔    li a { font-weight:bold;
 E > F  子元素选择器，匹配所有E元素的子元素F                            div > p { color:#f00; }
 E + F  毗邻元素选择器，匹配紧随E元素之后的同级元素F                  div + p { color:#f00; }  
```

```
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
    <style>

        .div1>p{
            background-color: aqua;
            color: deeppink;
        }

        .main2>div{
            background-color: blueviolet;
            color: chartreuse;
        }
    </style>
</head>
<body>

      <div class="div1">hello1
          <div class="div2">hello2
              <div>hello4</div>
              <p>hello5</p>
          </div>
          <p>hello3</p>
      </div>
      <p>hello6</p>

<hr>

     <div class="main2">1
       <div>2
           <div>
               4
           </div>
       </div>
       <div>
               3
           </div>
     </div>
</body>
</html>
```

注意**嵌套规则**：

1. 块级元素可以包含内联元素或**某些**块级元素，但内联元素不能包含块级元素，它只能包含其它内联元素。
2. **有几个特殊的块级元素只能包含内联元素，不能包含块级元素**。如h1,h2,h3,h4,h5,h6,p,dt
3. li内可以包含div
4. 块级元素与块级元素并列、内联元素与内联元素并列。

```
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
    <style>
        [suoning]{
            color: blueviolet;
        }
        .he>div{
            color: bisque;
        }

    </style>
</head>
<body>

<div class="he">111
    <p class="fr">222
        <div>333</div>
    </p>
    <div>444</div>
</div>


***************************

<div suoning="sb">ddd
         <p>pppp</p>
     </div>
     <p suoning="sb2">ddd2
         <p>pppp2</p>
     </p>
</body>
</html>
```

#### 3 属性选择器

多个属性

class = "div1 div2"表示设定css样式时，class属性为div1或div2均可

```
 E[att]         匹配所有具有att属性的E元素，不考虑它的值。（注意：E在此处可以省略，比如“[cheacked]”。以下同。）   p[title] { color:#f00; }

 
 E[att=val]     匹配所有att属性等于“val”的E元素                                 div[class=”error”] { color:#f00; }

 
 E[att~=val]    匹配所有att属性具有多个空格分隔的值、其中一个值等于“val”的E元素      td[class~=”name”] { color:#f00; }

 E[attr^=val]    匹配属性值以指定值开头的每个元素                     div[class^="test"]{background:#ffff00;}

 E[attr$=val]    匹配属性值以指定值结尾的每个元素                     div[class$="test"]{background:#ffff00;}

 E[attr*=val]    匹配属性值中包含指定值的每个元素                     div[class*="test"]{background:#ffff00;}
 
 为了将同时有 href 和 title 属性的 HTML 超链接的文本设置为红色，可以这样写：
a[href][title] {color:red;}

```

#### 4 伪类(Pseudo-classes)

CSS伪类是用来给选择器添加一些特殊效果。

**anchor伪类：**专用于控制链接的显示效果

```
a:link（没有接触过的链接）,用于定义了链接的常规状态。

a:hover（鼠标放在链接上的状态）,用于产生视觉效果。

a:visited（访问过的链接）,用于阅读文章，能清楚的判断已经访问过的链接。

a:active（在链接上按下鼠标时的状态）,用于表现鼠标按下时的链接状态。

伪类选择器 : 伪类指的是标签的不同状态:

a ==> 点过状态 没有点过的状态 鼠标悬浮状态 激活状态
```

```
<style type="text/css">
    a:link{
        color: red;
    }
    a:visited {
        color: blue;
    }
    a:hover {
        color: green;
    }
    a:active {
        color: yellow;
    }
</style>
</head>
<body>
    <a href="01-hello-world.html">hello-world</a>
</body>
</html>
```

**补充：**

```
.outer:hover .right{color: red}    outer下的right类进行渲染
```

**before after**伪类 ：

```
:before    p:before       在每个<p>元素之前插入内容
:after     p:after        在每个<p>元素之后插入内容
 p:before        在每个 <p> 元素的内容之前插入内容                    p:before{content:"hello";color:red}
 p:after         在每个 <p> 元素的内容之前插入内容                    p:after{ content:"hello"；color:red}
```

#### 5 css优先级和继承

**CSS优先级:**

所谓CSS优先级，即是指CSS样式在浏览器中被解析的先后顺序。

```
样式表中的特殊性描述了不同规则的相对权重，它的基本规则是：
      1 内联样式表的权值最高       style=""-------------------1000；
　　 2 统计选择符中的ID属性个数。    #id    －－－－－－－－－－－－－100
　　3 统计选择符中的CLASS属性个数。 .class  －－－－－－－－－－－－－10
 4 统计选择符中的HTML标签名个数。     p     －－－－－－－－－－－－－-1
```

按这些规则将数字符串逐位相加，就得到最终的权重，然后在比较取舍时按照从左到右的顺序逐位比较。

```
<style>
        #p{
            color: rebeccapurple;
        }
        .p{
            color: #2459a2;
        }
        p{
            color: yellow;
        }
    </style>
<p id="p" class="p" style="color: deeppink">hello yuan</p>
```

**CSS的继承性:** 

​      继承是CSS的一个主要特征，它是依赖于祖先-后代的关系的。继承是一种机制，它允许样式不仅可以应用于某个特定的元素，还可以应用于它的后代。例如一个BODY定义了的颜色值也会应用到段落的文本中。

```
body{color:red;}       <p>helloyuan</p>
```

这段文字都继承了由body {color:red;}样式定义的颜色。然而CSS继承性的权重是非常低的，是比普通元素的权重还要低的0。

```
p{color:green}
```

发现只需要给加个颜色值就能覆盖掉它继承的样式颜色。由此可见：任何显示申明的规则都可以覆盖其继承样式。　

​      此外，继承是CSS重要的一部分，我们甚至不用去考虑它为什么能够这样，但CSS继承也是有限制的。有一些属性不能被继承，如：border, margin, padding, background等。

```
div{
  border:1px solid #222
}

<div>hello <p>yuan</p> </div>
```

**附加说明:**

```
1、文内的样式优先级为1,0,0,0，所以始终高于外部定义。这里文内样式指形如<div style="color:red>blah</div>的样式，而外部定义指经由<link>或<style>卷标定义的规则。

　　2、有!important声明的规则高于一切。

　　3、如果!important声明冲突，则比较优先权。

　　4、如果优先权一样，则按照在源码中出现的顺序决定，后来者居上。

　　5、由继承而得到的样式没有specificity的计算，它低于一切其它规则(比如全局选择符*定义的规则)。
```



### 三  CSS的常用属性 

#### 1  颜色属性

```
<div style="color:blueviolet">ppppp</div>
 
<div style="color:#ffee33">ppppp</div>
 
<div style="color:rgb(255,0,0)">ppppp</div>
 
<div style="color:rgba(255,0,0,0.5)">ppppp</div>
```

#### 2  字体属性

```
font-size: 20px/50%/larger
 
font-family:'Lucida Bright'
 
font-weight: lighter/bold/border/
 
<h1 style="font-style: oblique">老男孩</h1>
```

#### 3  背景属性

```
background-color: cornflowerblue

background-image: url('1.jpg');

background-repeat: no-repeat;(repeat:平铺满)

background-position: right top（20px 20px）;(横向：left center right)(纵向：top center bottom)

      简写：<body style="background: 20px 20px no-repeat #ff4 url('1.jpg')">

              <div style="width: 300px;height: 300px;background: 20px 20px no-repeat #ff4 url('1.jpg')"> 
```

注意：如果将背景属性加在body上，要记得给body加上一个height，否则结果异常，这是因为body为空，无法撑起背景图片；另外，如果此时要设置一个width＝100px，你也看不出效果，除非你设置出html。   

```
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
    <style>
        html{
            background-color: antiquewhite;
            
        }
        body{
            width: 100px;
            height: 600px;
            background-color: deeppink;
            background-image: url(1.jpg);
            background-repeat: no-repeat;
            background-position: center center;
        }
    </style>
</head>
<body>

</body>
</html>
```

#### eg:

```
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>

    <style>

        span{
            display: inline-block;
            width: 18px;
            height: 20px;
            background-image: url("http://dig.chouti.com/images/icon_18_118.png?v=2.13");
            background-position: 0 -100px;
        }
    </style>
</head>
<body>


    <span></span>

</body>
</html>
```

#### 4  文本属性

```
font-size: 10px;
text-align: center;   横向排列
line-height: 200px;   文本行高 通俗的讲，文字高度加上文字上下的空白区域的高度 50%:基于字体大小的百分比
vertical-align:－4px  设置元素内容的垂直对齐方式 ,只对行内元素有效，对块级元素无效

text-indent: 150px;   首行缩进
letter-spacing: 10px;
word-spacing: 20px;
text-transform: capitalize;
```

#### 思考：

```
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
    <style>



        .outer .item {
            width: 300px;
            height: 200px;
            background-color: chartreuse;
            display: inline-block;
        }


    </style>
</head>
<body>
    <div class="outer">
        <div class="item" style="vertical-align: top">ll
        </div>
        <div class="item">
        </div>
    </div>

    <script>

    </script>
</body>
</html>
```

#### 5   边框属性

```
border-style: solid;
 
border-color: chartreuse;
 
border-width: 20px;
 
简写：border: 30px rebeccapurple solid;

控制某一条边：border-left
```

#### 6   列表属性

```
ul,ol{   list-style: decimal-leading-zero;
         list-style: none; <br>         list-style: circle;
         list-style: upper-alpha;
         list-style: disc; }
```

#### 7  dispaly属性

```
`none``block``inline`
```

display:inline-block可做列表布局，其中的类似于图片间的间隙小bug可以通过如下设置解决：

```
#outer{
        border: 3px dashed;
        word-spacing: -5px;}
```

#### 8  外边距和内边   

![img](image/877318-20161020102135748-1861483747.png)

- **margin:            用于控制元素与元素之间的距离；margin的最基本用途就是控制元素周围空间的间隔，从视觉角度上达到相互隔开的目的。**
- **padding:           用于控制内容与边框之间的距离；**   
- **Border(边框)     围绕在内边距和内容外的边框。**
- **Content(内容)   盒子的内容，显示文本和图像。**

元素的宽度和高度:

![Remark](image/lamp.gif)**重要:** 当您指定一个CSS元素的宽度和高度属性时，你只是设置内容区域的宽度和高度。要知道，完全大小的元素，你还必须添加填充，边框和边距。.

```
margin:10px 5px 15px 20px;-----------上 右 下 左
margin:10px 5px 15px;----------------上 右左 下
margin:10px 5px;---------------------上下  右左
margin:10px;    ---------------------上右下左
```

下面的例子中的元素的总宽度为300px：

```
width:250px;
padding:10px;
border:5px solid gray;
margin:10px;   
```

 练习： 300px*300px的盒子装着100px*100px的盒子，分别通过margin和padding设置将小盒子 移到大盒子的中间

```
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
    <style>

        .div1{
            background-color: aqua;
            width: 300px;
            height: 300px;
        }
        .div2{
            background-color: blueviolet;
            width: 100px;
            height: 100px;
        }
    </style>
</head>
<body>
       <div class="div1">
           <div class="div2"></div>
           <div class="div2"></div>
       </div>
</body>
</html>
```

思考1:

​       边框在默认情况下会定位于浏览器窗口的左上角，但是并没有紧贴着浏览器的窗口的边框，这是因为body本身也是一个盒子（外层还有html），在默认情况下，   body距离html会有若干像素的margin，具体数值因各个浏览器不尽相同，所以body中的盒子不会紧贴浏览器窗口的边框了，为了验证这一点，加上：

```
body{
    border: 1px solid;
    background-color: cadetblue;
}
```

\>>>>解决方法：

```
body{
    margin: 0;
}
```

思考2：

​           margin collapse（边界塌陷或者说边界重叠）

​          外边距的重叠只产生在普通流文档的上下外边距之间，这个看起来有点奇怪的规则，其实有其现实意义。设想，当我们上下排列一系列规则的块级元素（如段     落P）时，那么块元素之间因为外边距重叠的存在，段落之间就不会产生双倍的距离。又比如停车场

​           1兄弟div：上面div的margin-bottom和下面div的margin-top会塌陷，也就是会取上下两者margin里最大值作为显示值

​           2父子div    

​                   if  父级div中没有 border，padding，inline content，子级div的margin会一直向上找，直到找到某个标签包括border，padding，inline content              中的其中一个，然后按此div 进行margin ；

```
<!DOCTYPE html>
<html lang="en" style="padding: 0px">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
    <style>

        body{
            margin: 0px;
        }

        .div1{
            background-color: aqua;
            width: 300px;
            height: 300px;
        }
        .div2{
            background-color: blueviolet;
            width: 100px;
            height: 100px;
            margin: 20px;

        }
    </style>
</head>
<body>

<div style="background-color: cadetblue;width: 300px;height: 300px"></div>

       <div class="div1">
           <div class="div2"></div>
           <div class="div2"></div>
       </div>

</body>
</html>
```

 解决方法：

```
1: border:1px solid transparent
2:  padding:1px
3: over-flow:hidden; 
```

### 9 float属性

先来了解一下block元素和inline元素在文档流中的排列方式。

　　block元素通常被现实为独立的一块，独占一行，多个block元素会各自新起一行，默认block元素宽度自动填满其父元素宽度。block元素可以设置width、height、margin、padding属性；

　　inline元素不会独占一行，多个相邻的行内元素会排列在同一行里，直到一行排列不下，才会新换一行，其宽度随元素的内容而变化。inline元素设置width、height属性无效。inline元素的margin和padding属性。水平方向的padding-left, padding-right, margin-left, margin-right都产生边距效果；但竖直方向的padding-top, padding-bottom, margin-top, margin-bottom不会产生边距效果。

- 常见的块级元素有 div、form、table、p、pre、h1～h5、dl、ol、ul 等。
- 常见的内联元素有span、a、strong、em、label、input、select、textarea、img、br等

**所谓的文档流**，指的是元素排版布局过程中，元素会自动从左往右，从上往下的流式排列。

**脱离文档流**，也就是将元素从普通的布局排版中拿走，其他盒子在定位的时候，**会当做脱离文档流的元素不存在而进行定位**。

**只有绝对定位absolute和浮动float才会脱离文档流。**

​     ---部分无视和完全无视的区别？需要注意的是，使用float脱离文档流时，其他盒子会无视这个元素，但其他盒子内的文本依然会为这个元素让出位置，环绕在周围(可以说是部分无视)。而对于使用absolute position脱离文档流的元素，其他盒子与其他盒子内的文本都会无视它。(可以说是完全无视)

#### **浮动的表现**

​      定义：浮动的框可以向左或向右移动，直到它的外边缘碰到包含框或另一个浮动框的边框为止。由于浮动框不在文档的普通流中，所以文档的普通流中的浮动框之后的块框表现得就像浮动框不存在一样。（注意这里是块框而不是内联元素；浮动框只对它后面的元素造成影响）

**注意 当初float被设计的时候就是用来完成文本环绕的效果，所以文本不会被挡住，这是float的特性，即float是一种不彻底的脱离文档流方式。**无论多么复杂的布局，其基本出发点均是：“**如何在一行显示多个div元素**”。

**现象1:**

​      假如某个div元素A是浮动的，如果A元素上一个元素也是浮动的，那么A元素会跟随在上一个元素的后边(如果一行放不下这两个元素，那么A元素会被挤到下一行)；如果A元素上一个元素是标准流中的元素，那么A的相对垂直位置不会改变，也就是说A的顶部总是和上一个元素的底部对齐。此外，浮动的框之后的block元素元素会认为这个框不存在，但其中的文本依然会为这个元素让出位置。 浮动的框之后的inline元素，会为这个框空出位置，然后按顺序排列。

**现象2:**

(1)左右结构div盒子重叠现象，一般是由于相邻两个DIV一个使用浮动一个没有使用浮动。如上面的例1：相邻的两个盒子box2向左浮动、box3未浮动。一个使用浮动一个没有导致DIV不是在同个“平面”上，但内容不会照成覆盖现象，只有DIV形成覆盖现象。

解决方法：要么都不使用浮动；要么都使用float浮动；要么对没有使用float浮动的DIV设置margin样式。

(2)上下结构div盒子重叠现象

```
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
<style type="text/css">
         * {
             margin:0;padding:0;
         }
        .container{
            border:1px solid red;width:300px;
        }
        #box1{
            background-color:green;float:left;width:100px;height:100px;
        }
        #box2{
            background-color:deeppink; float:right;width:100px;height:100px; 
        }
         #box3{
             background-color:pink;height:40px;
         }
</style>
</head>
<body>

        <div class="container">
                <div id="box1">box1 向左浮动</div>
                <div id="box2">box2 向右浮动</div>
        </div>
        <div id="box3">box3</div>
</body>
</body>
</html>
```

例子如上：.container和box3的布局是上下结构，上图发现box3跑到了上面，与.container产生了重叠，但文本内容没有发生覆盖，只有div发生覆盖现象。这个原因是因为第一个大盒子里的子元素使用了浮动，脱离了文档流，导致.container没有被撑开。box3认为.container没有高度（未被撑开），因此跑上去了。

解决方法：

1、要么给.container设置固定高度，一般情况下文字内容不确定多少就不能设置固定高度，所以一般不能设置“.container”高度(当然能确定内容多高，这种情况下“.container是可以设置一个高度即可解决覆盖问题。

2、要么清除浮动。

#### **清除浮动：**

**在非IE浏览器（如Firefox）下，当容器的高度为auto，且容器的内容中有浮动（float为left或right）的元素，在这种情况下，容器的高度不能自动伸长以适应内容的高度，使得内容溢出到容器外面而影响（甚至破坏）布局的现象。这个现象叫浮动溢出，为了防止这个现象的出现而进行的CSS处理，就叫CSS清除浮动。**

clear语法：
clear : none | left | right | both

取值：
none : 默认值。允许两边都可以有浮动对象
left : 不允许左边有浮动对象
right : 不允许右边有浮动对象
both : 不允许有浮动对象

但是需要注意的是：clear属性只会对**自身**起作用，而不会影响其他元素。如果一个元素的右侧有一浮动对象，而这个元素设置了不允许右边有浮动对象，即clear：right，则这个元素会自动下移一格，达到本元素右边没有浮动对象的目的。

**方式1(推荐):**

```
.clearfix:after {             <----在类名为“clearfix”的元素内最后面加入内容； 
content: ".";                 <----内容为“.”就是一个英文的句号而已。也可以不写。 
display: block;               <----加入的这个元素转换为块级元素。 
clear: both;                  <----清除左右两边浮动。 
visibility: hidden;           <----可见度设为隐藏。注意它和display:none;是有区别的。visibility:hidden;仍然占据空间，只是看不到而已； 
line-height: 0;               <----行高为0； 
height: 0;                    <----高度为0； 
font-size:0;                  <----字体大小为0； 
} 
.clearfix { *zoom:1;}         <----这是针对于IE6的，因为IE6不支持:after伪类，这个神奇的zoom:1让IE6的元素可以清除浮动来包裹内部元素。
```

整段代码就相当于在浮动元素后面跟了个宽高为0的空div，然后设定它clear:both来达到清除浮动的效果。 
之所以用它，是因为，你不必在html文件中写入大量无意义的空标签，又能清除浮动。 

话说回来，你这段代码真是个累赘啊，这样写不利于维护。 
只要写一个.clearfix就行了，然后在需要清浮动的元素中 添加clearfix类名就好了。 
如：

```
<div class="head clearfix"></div>
```

**方式2：**

```
overflow:hidden;
```

**overflow**：hidden的含义是超出的部分要裁切隐藏，float的元素虽然不在普通流中，但是他是浮动在普通流之上的，可以把普通流元素+浮动元素想象成一个立方体。如果没有明确设定包含容器高度的情况下，它要计算内容的全部高度才能确定在什么位置hidden，这样浮动元素的高度就要被计算进去。这样包含容器就会被撑开，清除浮动。

### 10 position(定位)

#### **1 static**

**static 默认值，无定位，不能当作绝对定位的参照物，并且设置标签对象的left、top等值是不起作用的的。**

#### **2  position: relative／absolute**

​    **relative** 相对定位。相对定位是**相对于该元素在文档流中的原始位置**，即以自己原始位置为参照物。有趣的是，即使设定了元素的相对定位以及偏移值，元素还占有着原来的位置，即占据文档流空间**。对象遵循正常文档流，**但将依据top，right，bottom，left等属性在正常文档流中偏移位置。而其层叠通过z-index属性定义。

注意：position：relative的一个主要用法：方便绝对定位元素找到参照物。

####     **absolute** 绝对定位。

​      定义：设置为绝对定位的元素框从文档流完全删除，并**相对于最近的已定位祖先元素定位**，如果元素没有已定位的祖先元素，那么它的位置相对于最初的包含块（即body元素）。元素原先在正常文档流中所占的空间会关闭，就好像该元素原来不存在一样。元素定位后生成一个块级框，而不论原来它在正常流中生成何种类型的框。

重点：如果父级设置了position属性，例如position:relative;，那么子元素就会以父级的左上角为原始点进行定位。这样能很好的解决自适应网站的标签偏离问题，即父级为自适应的，那我子元素就设置position:absolute;父元素设置position:relative;，然后Top、Right、Bottom、Left用百分比宽度表示。

​      另外，对象脱离正常文档流，使用top，right，bottom，left等属性进行绝对定位。而其层叠通过z-index属性定义。

总结：参照物用相对定位，子元素用绝对定位，并且保证相对定位参照物不会偏移即可。

#### **3  position:fixed**

​        fixed：对象脱离正常文档流，使用top，right，bottom，left等属性以窗口为参考点进行定位，当出现滚动条时，对象不会随着滚动。而其层叠通过z-index属性 定义。 注意点： 一个元素若设置了 position:absolute | fixed; 则该元素就不能设置float。这 是一个常识性的知识点，因为这是两个不同的流，一个是浮动流，另一个是“定位流”。但是 relative 却可以。因为它原本所占的空间仍然占据文档流。

​       在理论上，被设置为fixed的元素会被定位于浏览器窗口的一个指定坐标，不论窗口是否滚动，它都会固定在这个位置。

#### 4 **仅使用margin属性布局绝对定位元素**

此情况，margin-bottom 和margin-right的值不再对文档流中的元素产生影响，因为该元素已经脱离了文档流。另外，不管它的祖先元素有没有定位，都是以文档流中原来所在的位置上偏移参照物。  
　　图9中，使用margin属性布局相对定位元素。
　　层级关系为：
```html
　　<div ——————————— position:relative;
　　<div—————————-没有设置为定位元素，不是参照物
　　<div———————-没有设置为定位元素，不是参照物
　　<div box1
　　<div box2 ——–position:absolute; margin-top:50px; margin-left:120px;
　　<div box3
　　效果图：
```
　　![img](image/292243208766594.jpg)

### 11 关于css的拾遗

#### 11.1 inline-block 的间隙





```
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
    <style>
        a{
            display: inline-block;
            background-color: #2459a2;
            width: 100px;
            height: 100px;
        }
    </style>
</head>
<body>

<a>111</a>
<a>222</a>
<a>333</a>

</body>
</html>
```



inline-block默认的空格符就是标签与标签之间的空隙造成的。

(1) 我们可以通过margin:-3px来解决，但是

1.我们布局肯定很多元素，不可能每个都添加margin负这样维护成本太大了

2.我们线上代码如果压缩，那么我们就不存在哪个4px的问题了，那么我们的margin负就回造成布局混乱！

(2)我们可以给几个标签加一个父级div，然后：

```
`div{word-spacing: -5px;}　　`
```

#### 11.2 word-wrap & **word-break:**

**word-wrap:**

the word-wrap CSS property is used to specify whether or not the browser is allowd to 
break lines within words in order to prevent overflow when an otherwise unbreakable 
string is too long to fit.

The `word-wrap` property was invented by Microsoft and added to CSS3. It allows long words to be able to be broken and wrap onto the next line. It takes in two values; `normal` or `break-word`.

**word-break:**

The word-break CSS property is used to specify how (or if) to break lines within words

```
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
    <style>


        .p1{
            width: 100px;
            background-color: #84a42b;
            word-wrap: break-word;
            word-break: break-all;
        }
        .p2{
            width: 100px;
            background-color: darkgoldenrod;
            word-wrap: break-word;
            word-break: break-all;
        }
    </style>
</head>
<body>


<p class="p1">hello yuan hello yuan hello yuan hello yuan hello yuan</p>

<p class="p2">helloyuanhelloyuanhelloyuanhelloyuanhelloyuanhelloyuan</p>


</body>
</html>
```



#### 1.3

​      一旦给元素加上absolute或float就相当于给元素加上了display:block;。什么意思呢？比如内联元素span默认宽度是自适应的，你给其加上width是不起作用的。要想width定宽，你需要将span设成display:block。但如果你给span加上absolute或float，那span的display属性自动就变成block，就可以指定width了。

#### 1.4 快写

```
div   tab   
a     tab
div.main>ul>li.c*4    tab
```

#### 1.5 display:flex

#### 1.6 响应式布局

#### 1.7水平居中

margin: 0 auto;

#### 1.8 float和position公用

https://www.cnblogs.com/coffeedeveloper/p/3145790.html#float

#### 1.9 连用

```html
background: url("img.png") no-repeat 0 -100px
```

