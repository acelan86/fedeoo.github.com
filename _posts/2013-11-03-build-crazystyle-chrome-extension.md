---
layout: post
title: "构建chrome插件"
description: ""
category: 
tags: []
excerpt: 先看的是chrome英文文档，后来发现360有中文文档<http://open.chrome.360.cn/extension_dev/overview.html>文档说的很大多很清楚。但是也有坑啊！360的没有更新的原因吧：有些方法已经有代替方法，如：+ chrome.tabs.sendRequest -->  chrome.tabs.sendMessage + chrome.tabs.getAllInWindow(null, function(tabs){}) --> chrome.tabs.query({}, function (tabs) {})关键是这些废弃的方法也不提个醒，用起来也没错。调试的时
---
{% include JB/setup %}
先看的是chrome英文文档，后来发现360有中文文档<http://open.chrome.360.cn/extension_dev/overview.html>

文档说的很大多很清楚。但是也有坑啊！360的没有更新的原因吧：
有些方法已经有代替方法，如：

+ chrome.tabs.sendRequest -->  chrome.tabs.sendMessage
+ chrome.tabs.getAllInWindow(null, function(tabs){}) --> chrome.tabs.query({}, function (tabs) {})

关键是这些废弃的方法也不提个醒，用起来也没错。调试的时候都不知道为什么不行。

## 调试：开发者模式

+ contentscript在每个页面执行，与每个页面的javascript同样的调试。
+ browserAction的调试在右键该图标审查。
+ background的调试在插件扩展程序下有检查视图 background page

可以看出各个还是分别在各处的。

之前犯的一个错误是在browserAction里绑定页签更新监听。在开发者模式，browserAction一直起作用的时候，还觉得正常。当browserAction不再处于活跃状态后页面更新新建就不再能够监听到了。这些方法应该放在background中。

还有就是：打包之前最好备份。