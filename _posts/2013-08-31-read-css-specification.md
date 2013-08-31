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


一些值得注意的 || 自己之前不晓得的 || 觉得重要的。

第四章 语法和基本数据类型 声明块中：当选择子中任何地方出错后，整个语句将被忽略。例如：
H3, H4 & H5 {color: red } H3将不起作用。