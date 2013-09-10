---
layout: post
title: "阅读CSS规范文档"
description: ""
category: 
tags: []
---
{% include JB/setup %}

在上次调试IE的时候，实在不明白为什么IE中一个元素会有offset值。拜读下规范文档。

<http://www.w3.org/TR/CSS2/>CSS2.0规范文档

<http://www.rsywx.net/wordpress/196/>中文翻译

---
---

一些值得注意的 || 自己之前不晓得的 || 觉得重要的。

####第四章 语法和基本数据类型 

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

###第八章 盒模型 
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

### 第十章 !important