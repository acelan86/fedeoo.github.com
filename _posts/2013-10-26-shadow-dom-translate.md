---
layout: post
title: "shadow DOM 文档翻译"
description: ""
category: 
tags: []
---
{% include JB/setup %}
文档地址：<http://www.w3.org/TR/shadow-dom/>(头次翻译技术文档，有点不忍看，不必要的内容不再翻译)
## 摘要
该规范描述了建立和维护DOM树之间功能边界的方法，以及在一个文档结构中这些文档树相互交互的方式。这样，为它们提供了更好的功能封装。
## 文档地位
草案意味着没有被W3C组织正式认可，有可能随时更新，甚至会被其它的文档取代。所以，在工作中参考引用这篇文档不是很合适。
## 简明目录
+ 1 关于文档
+ 2 Dependencies
+ 3 介绍
+   功能封装实例
+ 4 Shadow Trees
+   上界封装
+   下界封装
+   满足匹配标准
+   ::distributed() 伪元素
+   挂载多Shadow Trees
+   投影
+   组合
+   网状Shadow Trees
+   渲染Shadow Trees
+   自定义伪元素
+ 5 事件重定向
+   重定向相关目标
+   重定向焦点事件
+   一直停止的时间
+   事件分配
+   事件重定向实例
+ 6 样式
+   CSS变量
+   text-decoration属性
+   @host @-rule
+ 7 用户接口
+   范围和选择
+   焦点导航
+   活跃元素
+   编辑
+   辅助技术
+ 8 Shadow树中的HTML元素
+   内部HTML元素
+   HTML表单
+ 9 HTML元素和它们的Shadwo Trees
+ 10 元素和DOM对象
+   ShadowRoot对象
+   属性
+   方法
+   元素接口扩展
+   属性
+   方法
+   CSSHostRule 接口
+   CSSHostRule 属性
+   方法
    content HTML元素
    shadow HTML元素
+   shadow DOM实例

## 介绍
web应用开发者经常遇到需要封装DOM树的情况。尽管一个dom树是独立的，一般有许多功能树片段，并假定各个片段操作时独立。该文档称之为功能封装。与之相对的是信任封装。基于可信确保数据安全处理受限制信息。
功能封装主要关注在文档树中建立职能界限。一个功能边界是两个松散耦合的功能单元之间的功能划分问题。
## 功能封装实例
一个web应用用户界面通常由几个用户界面元素（或widgets---微件）组成，每个功能它独立的树。在这种情况下，当一个微件作为布置其它微件的容器时，就需要清楚的知道这个微件树的在哪儿结束和另一个微件树的在哪儿开始。
在文档树中一个微件添加、移动、删除时，就需要频繁检测文档树中的功能边界。除非微件用户确切的了解微件树是怎么设计的，否则让用户合理的操作widget简直不可能。微件开发者提供了一个典型的解决方法。------
## Shadow Trees
为了解决这些问题，引入了新的东东：Shadow DOM允许多个DOM树在渲染时组成一个更大的树。这些shadow树由一组建立封装边界的规则约束，并保留标准DOM的可组合语义。
在文档树和shadow树之间的封装边界我们称之为shadow边界。host shadow树的元素称为shadow hosts, shadow树的根称为shadow roots。渲染的时候，shadow树替换了shadow host的内容。

