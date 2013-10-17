---
layout: post
title: "前端模板"
description: ""
category: 
tags: []
---
{% include JB/setup %}
一遍讲设计模板引擎的文章，不过本身没有讲如何设计。
<http://www.toobug.net/article/how_to_design_front_end_template_engine.html>
或许每个人的叙述方式不同，不是很习惯别人的表述方式，还是按照自己的来。很多还是摘自其它文章。

    var content = "<table><tr><th>name</th><th>score</th></tr>";
    for (var i = Things.length; i >= 0; i--) {
        content += "<tr><td>" + Things[i].name + "</td><td>" + Things[i].score + "</td></tr>";
    }
    content += "</table>";
    document.getElementById('table').innerHTML = content;

引子：上面形式的代码应该都写过。smells bad! 太复杂且不利于阅读。使用引擎之后，可能是这样：(模板格式是自己随便写的，jekyll 使用的liquid模板，在这个项目中经常看到下面格式模板)

    <table>
        <tr><th>name</th><th>score</th></tr>
        {{ "{{ for student in Things" }} }}
        <tr><td>{{ "{% student.name" }} %}</td><td>{{ "{% student.score" }} %}</td></tr>
    </table>

之前算是学过jsp吧，对这样的标记并不陌生，<%= %> <% %>之间插入java代码，只不过jsp是编译成servlet的。好了，一个模板要达到的效果至少不能再让我用+连接字符串了。(adbox-Engine就有一个format方法做这种事，用于将html模板中的#{}替换为变量值。写代码不用想着模板不模板的，模式不模式的。)之后模板最好支持分支逻辑和循环遍历，上面的student.score可能需要根据值以不同颜色显示。另外，经常是数据的展现形式与数据不完全相同，比如日期date，输出的要求可能是('YYYY-MM-DD')，还有经常出现的bool值，可能存的是true false 或者 0 1 或者 yes no显示的时候又要显示为中文 是 否等。如果支持一个适配函数的形式就比较方便。

标记选取：{{"{{"}} }} {{ "<%=" }}" %>个人觉得只是约定形式。反倒习惯<%= %> <% %>这种。上面文章也提到避免与后端标记冲突，模板标记转义。  
模板位置：现在比较常见的是放在&lt;script&gt;标签中。web components组件规范中直接定义了一个template元素，不过并未广泛支持。
    
    <script type="text/tmpl" id="myTmpl">
        模板放这里......
    </script>

关于性能：文章指出了性能是个伪命题，也给了理由。但还是了解下artTemplate性能高效原因：<http://cdc.tencent.com/?p=5723>(原文有链接)

+ 预编译  各个模板简单说还是生产了一个js函数，预编译当然就是，，。
+ 更快的字符串相加方式  很多人误以为数组 push 方法拼接字符串会比 += 快，要知道这仅仅是 IE6-8 的浏览器下。实测表明现代浏览器使用 += 会比数组 push 方法快，而在 v8 引擎中，使用 += 方式比数组拼接快 4.7 倍。

关于异常处理：就是模板函数执行错误时，artTemplate能指定到行号。文中还给出了一个简单的demo方案。就是在生成的代码中每行插入$liine = currentLine这样的东西，感觉很老土，不知道artTemplate是不是采用的这种方式。


jQuery 作者 john 开发的微型模板引 <http://ejohn.org/blog/javascript-micro-templating/>

    // Simple JavaScript Templating
    // John Resig - http://ejohn.org/ - MIT Licensed
    (function(){
      var cache = {};
     
      this.tmpl = function tmpl(str, data){
        // Figure out if we're getting a template, or if we need to
        // load the template - and be sure to cache the result.
        var fn = !/\W/.test(str) ?
          cache[str] = cache[str] ||
            tmpl(document.getElementById(str).innerHTML) :
         
          // Generate a reusable function that will serve as a template
          // generator (and which will be cached).
          new Function("obj",
            "var p=[],print=function(){p.push.apply(p,arguments);};" +
           
            // Introduce the data as local variables using with(){}
            "with(obj){p.push('" +
           
            // Convert the template into pure JavaScript
            str
              .replace(/[\r\t\n]/g, " ")
              .split("<%").join("\t")
              .replace(/((^|%>)[^\t]*)'/g, "$1\r")
              .replace(/\t=(.*?)%>/g, "',$1,'")
              .split("\t").join("');")
              .split("%>").join("p.push('")
              .split("\r").join("\\'")
          + "');}return p.join('');");
       
        // Provide some basic currying to the user
        return data ? fn( data ) : fn;
      };
    })();


以下面的模板为例：

    <h3>
        <% if (typeof content === 'string') { %>
        <%= content %>
        <% } %>
    </h3>

解析之后生成的函数类似：

    function anonymous(obj/**/) {
        var p=[];
        with(obj){
            p.push(' <h3> '); 
            if (typeof content === 'string') {
                p.push(' ', content ,' '); 
            } 
            p.push(' </h3> ');
        }
        return p.join('');
    }

刚给出的模板引擎正则表达式那段不大好看，就是将<% %>里面的内容直接作为字符串，其它的正文则是压入结果数组。我自己稍微改了下：

    var cache = {};
    this.tmpl = function (str, data) {
        var fn = !/\W/.test(str) ?  cache[str] = cache[str] || tmpl(document.getElementById(str).innerHTML) :
            new Function("obj" , "var p = []; with(obj){" +
                 format(str) + 
            "};return p.join('');");
        return data ? fn(data) : fn;

    };
    function format (str) {
        str = '%>' + str + '<%';
        return str.replace(/[\r\n\t]/g, ' ')
        .replace(/<%=(.*?)%>/g, "',$1,'") 
        .split("<%").join("');")
        .split("%>").join("p.push('");
    }

测试页面在：<http://fiddle.jshell.net/fedeoo/6jwMy/2/show/>
