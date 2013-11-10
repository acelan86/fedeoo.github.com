---
layout: post
title: "阅读CSS规范文档"
description: ""
category: 
tags: [阅读文档记录]
excerpt: 一些值得注意的 || 自己之前不晓得的 || 觉得重要的。### 第四章 语法和基本数据类型 
---
{% include JB/setup %}

在上次调试IE的时候，实在不明白为什么IE中一个元素会有offset值。拜读下规范文档。

<http://www.w3.org/TR/CSS2/>CSS2.0规范文档

<http://www.rsywx.net/wordpress/196/>中文翻译

---
---

一些值得注意的 || 自己之前不晓得的 || 觉得重要的。

### 第四章 语法和基本数据类型 

声明块中：当选择子中任何地方出错后，整个语句将被忽略。例如：
H3, H4 & H5 {color: red } H3将不起作用。

解析错误处理规则
非法值。用户端必须忽略带有非法值的声明。例如：

	IMG { float: left }       /* 正确的CSS2 */
	IMG { float: left here }  /* "here"不是'float'的值 */
	IMG { background: "red" } /* CSS2中关键字不可以加引号 */
	IMG { border-width: 3 }   /* 长度值必须指定单位 */

CSS2解析器将保留第一条规则并忽略其它的规则，就如同：

	IMG { float: left }
	IMG { }
	IMG { }
	IMG { }

 子元素并不继承由其父元素指定的相对值，它们（通常）继承计算值。</br>
 下面的规则中，计算出的H1元素的'text-indent'值是36pt，而不是45pt，如果H1是BODY元素的子元素的话。

	BODY {
	  font-size: 12pt;
	  text-indent: 3em;  /* i.e., 36pt */
	}
	H1 { font-size: 15pt }

### 第六章 指定属性值 
大部分情形下，元素继承计算值。另，实际值可能与计算值不同的表现是：如在一些机器上font-size:11px;与font-size:12px;是同样的字体大小。

### 第八章 盒模型 
(在中文翻译中，box给翻译成了包含框，有点不习惯)
盒的不同区域背景样式是这样决定的：

+ 内容区：产生该区域的元素的'background'属性。
+ 边白区：产生该区域的元素的'background'属性。
+ 边框区：产生该区域的元素的边框属性。
+ 边距区：边距总是透明的。

注意padding区域产生'background'属性。一次使用background时，因为IE6下双边距问题，需要将margin改为padding时发现，背景图在padding区域开始绘制。

边距属性
百分比的计算基于生成的框的包含块的宽度。padding百分比值计算也是基于包含块。
如果仅有一个值，它将应用于所有四边。如果有两个值，顶底边距设置为第一个值，左右边距设置为第二个值。如果有三个值，顶边距设置为第一个值，左右边距设置为第二个值，底边距设置为第三个值。如果有四个值，它们分别设置顶，右，底，左边距。
垂直边距重合
em常规流em向中两个或多个块框相邻的垂直边距会重合。结果的边距宽度是相邻边距宽度中较大的值。如果出现负边距，则在最大的正边距中减去绝对值最大的负边距。如果没有正边距，则从零中减去绝对值最大的负边距。

由于在某种程度上，属性具有覆盖的特性，因此指定规则的顺序很重要。
考虑这样一个例子：

	BLOCKQUOTE {
	  border-color: red;
	  border-left: double;
	  color: black
	}

在上例中，左边框的颜色是黑色，而其它边框的颜色是红色。
这是因为'border-left'设置了宽度，样式和颜色。由于'border-left'属性并没有给出颜色值，
它将从'color'属性中获得值，并与'color'属性设置在'border-left'之后这一事实无关。

### 第九章 !important

#### 9.2 控制包含框
如果一个块控制框（如上例中为DIV生成的框）在其中包含另外一个块框（如上例中的P），我们强迫它在其内只包含块框，而将任何的行内框都包含在一个匿名块框之内。

	<div>
	This is anonymous text before the P.
	<P>This is the content of P.</>
	This is anonymous text after the P.
	<div>

	<P>Some <EM>emphasized</em> text</P>

不知道上面的匿名块控制框和匿名行内控制框在什么时候会有表现的不同。

#### 9.2.3 压缩的控制框
如果一个块框（常规流向）跟随在一个压缩的控制框之后，则压缩的控制框格式化为一个单行的行内框。
给出的例子第一个dd 和 dt在同一行。但是测试的结果却是dd在一单独行表现为块框。

#### 9.3 position

#### 9.5浮动
浮动框的顶部和当前线框的顶部对齐（如果没有线框，则对齐前面一个块框的底部）。在浮动框之前当前行的任何内容将重新排列在浮动框另一边第一个可用的行上。  
一个浮动可以覆盖常规流向中其它的框（如与浮动相邻的常规流向框的边距为负数）。如果一个行内框覆盖一个浮动框，该行内框的内容、背景和边框在该浮动前面渲染。如果一个块框覆盖一个浮动框，块框的背景和边框在浮动后面渲染，并只有在浮动为透明时才可见。块框的内容在浮动前面渲染。ps:这一段不是很明白。没有做出覆盖的效果。  
后面就是大家耳熟能详的position部分。

### 第十章 !important

#### 10.3 宽度和边距计算  
对行内元素来说：width属性并不适用于非替换的行内元素。对'left'，'right'，'margin-left'或'margin-right'指定'auto'的效果是计算值为'0'。  
常规流向中的块类、非替换元素：  
如果'left'或'right'设置为'auto'，它们的计算值为0。在其它属性间必须保持下面的约束：  
'margin-left' + 'border-left-width' + 'padding-left' + 'width' + 'padding-right' + 'border-right-width' + 'margin-right' = 包含块的宽度  
（如果边框的样式为'none'，边框宽度认为是'0'。）

+ 如果上述所有的属性指定了非'auto'的值，这些值被称为是“过度约束”，其中之一的计算值将不得不和它的指定值不同。如果'direction'属性为'ltr'，'margin-right'的指定值被忽略，并重新计算以满足上面的等式。
+ 如果'direction'为'rtl'，对'margin-left'采取上述的方法。
+ 如果只有一个值指定为'auto'，它的计算值从等式中得出。
+ 如果'width'设置为'auto'，则其它的'auto'值成为'0'而'width'从等式的剩余部分得到。
+ 如果'margin-left'和'margin-right'为'auto'，它们的计算值相同。
替换元素的width如果指定为'auto',值为内在宽度。

浮动元素：  
如果'left'，'right'，'width'，'margin-left'或'margin-right'指定为'auto'，它们的计算值为'0'。

绝对定位元素  
约束：'left' + 'margin-left' + 'border-left-width' + 'padding-left' + 'width' + 'padding-right' + 'border-right-width' + 'margin-right' + 'right' = 包含块的宽度  
 （如果边框样式为'none'，边框宽度认为是'0'。这一约束的实现通过如下顺序的一系列替换而达到：

+ 如果'left'设置为'auto'而'direction'为'ltr'，替换'auto'为：包含块左边到假定它的'position'属性设置为'static'，而生成元素的第一个框的那个假想框的左边距边的距离。（但是为了不去实际计算那个框，用户端可以自由地猜想它可能在的位置。）如果假想框在包含块的左边，该值为负数。
+ 如果'right'设置为'auto'而'direction'为'rtl'，替换'auto'为：包含块右边到上述提到的那个假想框的右边距边的距离。如果假想框在包含块边的左边，该值为正数。
+ 如果'width'为'auto'，将剩余'left'或'right'的'auto'替换为'0'。
+ 如果'left'，'right'或'width'还是'auto'，将所有'margin-left'或'margin-right'的'auto'替换为'0'。
+ 如果在这一步'margin-left'和'margin-right'还是'auto'，基于额外的约束即两个边距必须相同，解出等式。
+ 如果在这一步只有一个'auto'存在，根据等式解出那个值。
+ 如果在这一步各值间过度约束，要么忽略'left'的值（如果'direction'为'rtl'），要么忽略'right'的值（如果'direction'为'ltr'），然后解出那个值。

min-width以及max-width这两个属性如何影响'width'属性的计算值：  

先根据上述“宽度和边距的计算”中的规则计算宽度（不考虑'min-width'及'max-width'）。 

+ 如果计算得到的'min-width'大于'max-width'，'max-width'设置为'min-width'的值。0  
+ 如果计算值大于'max-width'，上述规则再次使用，但是这次用'max-width'作为'width'的指定值。  
+ 如果计算值小于'min-width'，上述规则再次使用，但是这次用'min-width'作为'width'的指定值。    


#### 10.5 高度
height属性： 指定了块类元素和替换元素生成的框的内容高度。非替换行内元素框的高度由元素的'line-height'值（或继承值）决定。  
行内元素： 如果'top'，'bottom'，'margin-top'或'margin-bottom'为'auto'，它们的计算值为0。  
常规流向中的块类、非替换元素，浮动的非替换元素：  
如果'top'，'bottom'，'margin-top'或'margin-bottom'为'auto'，它们的计算值为0。如果'height'为'auto'，高度取决于该元素是否有任何块类子元素。如果它只包含行内子元素，高度等于最顶端的线框的顶到最底端的线框的底之间的距离。如果它包含块类子元素，高度等于最顶端的块类子框的顶边界到最底端的块类子框的底边界。只考虑常规流向中的子元素（即，浮动框和绝对定位框被忽略，只考虑相对定位框未偏移的情况）。注意，子框可以是一个匿名框。
绝对定位的非替换元素

 对于绝对定位的元素，垂直方向的尺寸必须满足如下约束：
'top' + 'margin-top' + 'border-top-width' + 'padding-top' + 'height' + 'padding-bottom' + 'border-bottom-width' + 'margin-bottom' + 'bottom' = 包含块的高度
（如果边框样式为'none'，认为宽度是'0'。）该约束的实现基于下列顺序的一系列替换：  

+ 如果'top'为'auto'，将它替换为：包含块的顶边到假定它的'position'属性设置为'static'，而生成元素的第一个框的那个假想框的顶边距边的距离。（但是为了不去实际计算那个框，用户端可以自由地猜想它可能在的位置。）如果假想框在包含块上面，该值为负数。
+ 如果'height'和'bottom'为'auto'，将'bottom'替换为0。
+ 如果'bottom'或'height'还是'auto'，将'margin-top'或'margin-bottom'中的'auto'替换为'0'。
+ 如果在这一步，'margin-top'和'margin-bottom'还是'auto'，根据附加的约束，即这两个边距必须相等来解出等式。
+ 如果在这一步，只有一个'auto'，根据等式解出该值。
+ 如果在这一步各值间过度约束，忽略'bottom'的值，然后解出那个值。

从上面规则周看，高度计算可以 margin：auto?垂直居中。height自适应？首先height的高度还是根据子元素计算出来，而不是按照约束。翻译中漏掉了按照10.6.7计算高度。margin垂直居中肯定可以，bottom不能为auto，bottom指定值与top相同的值就可以。

同理：min-height及max-height这两个属性如何影响'height'属性的计算值：  

高度的计算基于上述“高度和边距的计算”的规则（不考虑'min-height'及'max-height'）。

+ 如果'min-height'的计算值大于'max-height'，'max-height'设置为'min-height'的值。
+ 如果计算的高度大于'max-height'，则上述规则再次使用，但是这次用'max-height'作为'height'的指定值。
+ 如果计算高度小于'min-height'，则上述规则再次使用，但是这次用'min-height'所谓'height'的指定值。

### 表格
表格中实现带有行间水平分割线的表格是方案一，测试不得效果。且在IE下tr没有边框。或被td覆盖。

之前经常遇到 ul li 在IE7以及以下的各种麻烦事。没有记录。   
正则表达式问题：
<***#{a}**#{b}*>需要将'#{a}'替换为'+a+'....编写程序，可以先匹配&lt;&gt;再replace#{a};如果在编辑器中使用一条正则表达式如何写：
	(<[^>]*?)#(\{([^\}]*)\}.*)* $1$2  