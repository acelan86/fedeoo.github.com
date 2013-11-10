---
layout: post
title: "代码评审-slider.js"
description: ""
category: 
tags: []
excerpt: 原代码在codereview项目中 review建议：第86行：格式问题，运算符两侧加空格，语句结尾严格使用”;” ,   如：return \“a\” + \“b\” + \“c\”;2.  188-189： 复杂操作缺乏注释，折行错误，导致代码意义不能明确看出3.  254：添加多个css时应该批量处理，而不是一个属性一个属性加，提高效率，如：dom.style.cssText += ‘;width\:100px;height\:200px’;4.  264：没用的代码请及时清除5.  304：判断是否有某个className时不能直接使用空格进行split，如果className用多个空格连接则会出问题，建议使用正则判断或者在需要判断的className两侧加上空格进行判断。如：(’  ’ + dom.className + ‘  ’).indexOf(‘  ’ + cName + ‘ ’) !== -1;
---
{% include JB/setup %}
原代码在codereview项目中
review建议：

1.  第86行：格式问题，运算符两侧加空格，语句结尾严格使用”;” ,   如：return “a” + “b” + “c”;
2.  188-189:  复杂操作缺乏注释，折行错误，导致代码意义不能明确看出
3.  254：添加多个css时应该批量处理，而不是一个属性一个属性加，提高效率，如：dom.style.cssText += ‘;width:100px;height:200px’;
4.  264：没用的代码请及时清除
5.  304：判断是否有某个className时不能直接使用空格进行split，如果className用多个空格连接则会出问题，建议使用正则判断或者在需要判断的className两侧加上空格进行判断。
如：(’  ’ + dom.className + ‘  ’).indexOf(‘  ’ + cName + ‘ ’) !== -1;
6.  306：代码有bug。
7.  375：相等条件判断，如没有特殊需要（如特地要让undefined == null == ‘‘ == 0等），必须使用严格相等===
8.  405：注释应该写清楚意思。


代码修改：


1. 格式应该注意，另没有使用jshint工具审查。

2. 代码调整后：

        var mainContainner = $('<div style="position:absolute;">')//主图区域DOM结构
            .append(this.main = $('<div class="com-simpleslider-main" style="width:100%;height:100%;">'))
            .css({width : options.mainWidth, height : options.mainHeight})
            .resizable({
                resize : this._resizingMainHandler()
            });
        this.indicator = $('<div class="com-simpleslider-indicator" style="position:absolute;">')//指示区域DOM结构
            .append(this.larrow = $('<div class="com-simpleslider-larrow com-simplesilder-indicator-bgimg">'))
            .append(this.rarrow = $('<div class="com-simpleslider-rarrow com-simplesilder-indicator-bgimg">'))
            .append($('<div class="com-simpleslider-indicator-inner">')
                .append(this.indicatorCnt = $('<div class="com-simpleslider-indicator-cnt">')))
            .draggable({
                containment : this.view,
                drag : this._dragingIndicatorHandler()
            })
            .resizable({
                handle : 'e, s',
                resize : this._resizingIndicatorHandler()
            })
        this.view.append(mainContainner).append(this.indicator);

3. 这个问题，后面也有几处，代码调整后：

        indicatorInner.css({
            'height' : indicatorInnerHeight,
            'width' : ''
        });

5. 这一处代码作用是移去已加的slider-style的样式，removeClass()不接受正则，所以先取到所有的匹配的className。
之前没有问题是因为，对classes可能为空串的某项只做了正则匹配，之前的正则没出错是因为/slider-style1\*/.test() 也匹配slider-style-\*这种样式。

        var classes = this.indicator[0].className.split(/\d+/);
        for (var i = 0, len = classes.length; i < len; i++) {
            if (/^slider-style.*/.test( classes[i])) {
                styleClass += classes[i] +' ';
            }
        }

7. 代码格式问题line:335 调整规范格式

        indicatorItems.push(
        '<div  data-idx="' + i + '" class="com-simpleslider-indicator-item com-simplesilder-indicator-bgimg item-idx-' + (i + 1) + '">',
            '<span class="number">' + (i + 1) + '</span>',
        '</div>');
