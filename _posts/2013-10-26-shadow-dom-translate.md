---
layout: post
title: "shadow DOM 文档翻译"
description: ""
category: 
tags: []
excerpt: 文档地址：http://www.w3.org/TR/shadow-dom/(头次翻译技术文档，有点不忍看，不必要的内容不再翻译)## 摘要该规范描述了建立和维护DOM树之间功能边界的方法，以及在一个文档结构中这些文档树相互交互的方式。这样，为它们提供了更好的功能封装。##文档地位 草案意味着没有被W3C组织正式认可，有可能随时更新，甚至会被其它的文档取代。所以，在工作中参考引用这篇文档不是很合适。
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
为了解决这些问题，引入了新的abstra东东：Shadow DOM允许多个DOM树在渲染时组成一个更大的树。这些shadow树由一组建立封装边界的规则约束，并保留标准DOM的可组合语义。
在文档树和shadow树之间的封装边界我们称之为shadow边界。host shadow树的元素称为shadow hosts, shadow树的根称为shadow roots。渲染的时候，shadow树替换了shadow host的内容。
为了组织shadow树和shadow host的子元素，我们引入插入点的概念。插入点是定义在shadow tree中，在渲染的时候shadow host的子元素转到哪儿，决定哪个子元素被转到哪个插入点的机制称为分配-distribution。
这样，一个shadow树的封装可以被看做双重的问题
1.上界封装  管理shadow root与shadow host之间的边界
2.下界封装  管理插入点和shadow host子元素之间的边界。
## 上界封装
为维护上界封装，在shadow树中的所有节点必须满足以下约束：
+ownerDocument属性指向shadow host的document.
+在shadow host的不能通过DOM树访问到shadow树中的节点和命名元素的
+在文档的NodeList,HTMLColllection或者DomElementMap实例中这些节点都是不存在的。
+即使这些节点有唯一的ID命名元素也不能在shadow host的文档中访问到。
+shadow host不能访问节点的样式表
+shadow root可以使用DOM树访问方法访问这些节点
+在shadow树可寻到的拥有唯一ID命名元素的节点
+选择器一定不能跨越文档树与shadow树的shadow边界
简单点说，就是shadow root提供了一组DOM树访问方法，只有使用这些方法shadow root的后代元素才能被访问到。
shadow root的parentNode和parentElement属性必须一致返回null。
## 下界封装
为了维护下界封装，分配到shadow tree上插入点位置的shadow host的子节点必须满足以下约束：
+分配不能影响文档树或shadow树
+每个参与分配的插入点为子元素低通一种匹配标准，该标准决定一个给定节点是否能够分配到给定的插入点。
+分配的算法必须稳定
+分配本身不能改变影响分配的变量
+任何时候变量变化都将立即重新分配
一个插入点可能是活跃的也可能不是，一个活跃的插入点参与分配过程，非活跃的插入点并不参与。如果没有明确指出，一个插入点将作为活跃的考虑。
如果一个插入点并不在shadow tree中，它将按照HTMLUnknownElement渲染。
分配算法按照以下步骤执行
输入
TREE, shadow tree
pool DOM节点列表
输出
缓冲池中的节点将会被分配到树的插入点中。
1 Repeat for each active insertion point in TREE, in tree order:
    1.Let POINT be the current insertion point
    2.Repeat for each node in POOL:
        1.Let NODE be the current node
        2.If the NODE satisfies POINT's matching criteria:
            Distribute the NODE to POINT
            Remove NODE from the POOL
        3.Otherwise, continue to repeat
    3.Continue to repeat
## 满足匹配标准
针对插入点的匹配标准是一组复合选择器。这些复合选择器受限于下列简单选择器：
+类型选择器
+类选择
+ID选择
+属性选择
如果节点满足以下条件就认为是符合标准的：
+所有的复合选择子只能包含上面指定的简单选择器
+一个节点必须至少匹配其中一个复合选择器，或者集合为空。
::distributed() 伪元素
::distributed(selector)接收相对选择子作为参数。它表示一个插入点和分配到插入点的子元素之间的关系。这个参数
例如，下面这个选择器表示选择分配在插入点上的该元素的所有div子元素。
::distributed(div)
下面的选择器表示所有的分配在插入点上的带有victory属性的span子元素
应该引用设置在插入点或父节点上的元素吗？
其它类型的选择器都不能跨越shadow边界。
挂载多个Shadow Trees
一个shadow host可能挂载不止一个shadow tree。在这种情况下，这些树加入host依照堆栈顺序，从最近加入的树开始。这个集合称为树栈，最先加入的树是younger tree，次之的是older tree。最近加入的树是youngest tree。
为了在同一个host中组合多个shadow trees, 定义了特殊的插入点，shadow 插入点在渲染的时候为older tree在shadow tree中指定一个位置。如果存在多个多个shadow插入点，只有第一个能够标示出。也就是说，shadow tree指定shadow 插入点。
同插入点类似，shadow 插入点可能是活跃的或非活跃的。
## 投影
值得特别注意的情形是，插入点是另一个shadow host的子元素。在这种情形下，分配到插入点的节点就像它们是shadow host的子节点。这样，分配到shadow树上的节点已经被嵌套树分配。一个节点被分配到多个插入点的过程就称为投影。
尽管在投影钟可以分配不止一个插入点，但一个节点仍然只会渲染一次，因为：插入点是一个shadow host的子节点事，将不再渲染。shadow树。
## 组合
对给定shadow host，shadow 树的组合按照树组合算法，处理过程大概如下：
输入：
HOST, shadow host
输出：

+树在栈顶
+pool置空
对每个host的子节点
    当前子节点
    如果节点是插入点
        让已投影节点对了，分配插入点按照树合成算法
        如果投影是空列表，对它子节点设置列表
        添加投影的到缓冲池
    添加子元素到缓冲池
对Tree重复：
Let POINT be the first encountered active shadow insertion point in TREE, in tree order
Run the distribution algorithm, supplying POOL and TREE as input
If POINT exists:
    Find the next older tree, relative to TREE in the HOST's tree stack
        If there is no older tree, stop.
        Otherwise:
            Set TREE to be this older tree
            Assign TREE to the POINT
            Continue to repeat
Otherwise, stop.
当插入点或shadow插入点没有被指派任何东西时，在渲染时，。fallback content是插入点的后代元素。在fallback content的插入点或shadow插入点将被当做非活跃的。
## 嵌套shadow trees
shadow树中的任何元素都可能是一个shadow host，因此，就会产生嵌套shadow树。如果一个shadow树的shadow host是它自身的一部分，我们就说它是嵌套的。如果树B嵌套在树A中，shadow树A嵌套shadow树B。如果shadow host声明在文档树中，该文档就说它shadow树的嵌套树。
一个shadow树**********什么什么封闭的 balabala不知道怎么翻译，给你们看看原文吧：
A shadow tree is enclosed by another shadow tree when the enclosing tree nests the enclosed tree through one or more levels of nesting. All shadow trees are enclosed by the document tree.
渲染Shadow Trees
按照以下步骤：
输入
Host, shadow host
输出
host的渲染，包括shadow tree
+对给定的shadow host调用树组合算法
+作为shadow host的内容，渲染最新的树作为DOM树，
每个shadow host的元素相应渲染
在插入点的位置，按下面的步骤渲染插入点：
如果插入点上分配的有子节点，对每个分配的子节点：
让子节点称为被渲染的节点
如果该节点本身是插入点，渲染的树是嵌套的，使用插入点渲染步骤渲染
否则 是子节点作为任何DOM树
否则渲染fallback内容作为任何 DOM树
在每个shadow插入点的位置：
如果有旧树，分配给shadow插入点，依照渲染DOM树的方式渲染，但
否则 渲染fallback content
渲染的结果生成几个DOM树的组合结构，包括文档树，术语‘as rendered’指的就是。
## 自定义伪元素

在某些情况下，一个shadow树开发者可能希望指定一个或多个元素从树上作为结构对象，提供关于shadow树内容的附加信息。
例如，当开发二维范围滑动微件时，可以在两个方向移动的那个。开发者想让用户直接设置它的样式。
自定义伪元素满足了这种需求。
自定义伪元素关联shadow树中的元素和字符串值。一个自定义伪元素必须以x-开始，否则视为无效。
在上面提到的二维滑动控件，开发者可以这样构建该微件的shadow树。

    var widget = document.createElement('div');
    var root = widget.createShadowRoot();
    var thumb = document.createElement('div');
    thumb.pseudo = 'x-thumb';
    root.appendChild(thumb);

一旦设置了伪属性，微件使用者就可以使用x-thumb伪元素自定义微件的样式，直接接触到微件的shadow树。
    div::x-thumb {
        width: 10px;
        height: 10px;
        color: bisque;
    }
    使用x-前缀引入了一个问题（见bug 20600）,但是目前并没有一个更好的选择。
## 事件
在shadow树中事件分配，它的路径有可能终止在shadow边界，也有可能穿越边界。mutation事件是一例外，这个事件类型不会分派到shadow树上。
对跨越shadow边界的事件，它必须位于调整的父节点，或与父节点相同渲染方式的节点。parent calculation algorithm 用于觉得已给节点的调整的父节点，创建事件分派的祖先列表。这个算法处理过程大概如下：
输入
节点
上下文
输出
parent 一个节点的调整父节点
如果节点是shadow root
如果节点分派到shadow插入点
让parent为分派节点的shadow插入点
否则，让让parent 节点的shadow host
如果该节点是shadow树上分配的插入点，设置parent为分配节点的插入点
如果节点是插入点，并且context不为null：
如果节点的父节点是shadow host并且shadow树包含分派上下文的插入点，设置parent为插入点
否则，设置parent为节点的父节点。
##事件重定向
事件跨越shadow边界情况下，事件的target信息将会调整，为了维护上界封装。事件重定向是计算相对target的过程，对事件分配节点的每个祖先节点。相对target在维护上界封装准确表示在给定祖先的已分派事件的target。
重定向算法用于觉得相对target,步骤流程如下：
Input
NODE, a node
Output
TARGETS, a list of tuples, each containing NODE's ancestor and its relative target
Let STACK be a stack of nodes
Let ANCESTOR be NODE
Repeat while ANCESTOR exists:
If ANCESTOR is an insertion point:
Let CONTEXT be the top-most item in STACK that is not an insertion point or null if such item does not exist.
Let TOP be the item at the top of STACK, or ANCESTOR if STACK is empty
Push TOP into STACK
Otherwise, let CONTEXT be null
If STACK is empty, push ANCESTOR into STACK
Let TARGET be the node at the top of STACK
Add (TARGET, ANCESTOR) tuple to TARGETS
If ANCESTOR is a shadow root:
Pop STACK, if STACK is not empty
Set ANCESTOR to be the result of parent calculation algorithm, given ANCESTOR and CONTEXT as input
重定向过程优先于事件分配过程。
重定向相关target
一些事件有relatedTarget属性，该属性指的节点并不是事件的target，但是与事件相关。
例如，mouseover事件的relatedTarget可能保存了鼠标从该节点移到事件target。relatedTarget在shadow树中，确保UA不会在该树之外泄露它的真实值。如果relatedTarget和target是同一shadow树的部分，确保UA会在边界停止事件以避免在同一节点触发mouseover和mouseout事件。
这样，如果一个事件有relatedTarget，它的值和事件分派必须调整。一般：
对给定节点，relatedTarget必须改变到它的在同一shadow树的祖先或自身，
事件监听不能在一个target和relatedTarget相同的节点上调用。
相关目标重置算法决定relatedTarget属性值，按照以下步骤：
Input
NODE, the node on which event listeners would be invoked
RELATED, the related target for the event
Output
ADJUSTED, the adjusted related target for NODE
Let TARGET be NODE
Let ADJUSTED be undefined
Repeat while TARGET exists:
Let STACK be an empty stack of nodes
Let ANCESTOR be RELATED
Let LAST be undefined
Repeat while ANCESTOR exists:
Let CONTEXT be null
If STACK is empty, push ANCESTOR into STACK
Otherwise, if ANCESTOR is an insertion point:
Let CONTEXT be the top-most item that is not an insertion point in STACK or null, if such item does not exist.
If LAST is distributed or assigned into ANCESTOR:
Let HEAD be the node at the top of the STACK
Push HEAD into STACK
If ANCESTOR and TARGET are in the same tree:
Let ADJUSTED be the node at the top of the stack
Stop.
If ANCESTOR is a shadow root, pop STACK
Let LAST be ANCESTOR
Set ANCESTOR to be the result of parent calculation algorithm, given ANCESTOR and CONTEXT as input
If TARGET is a shadow root, let TARGET be the shadow host of TARGET
Otherwise, let TARGET be TARGET's parent node
重定向焦点事件
focus DOMFocusIn blur 和DOMFocusOut事件与relatedTarget事件的处理方式相同，在相应的节点由于目标获得或失去焦点，从而导致目标行为的不确定。
一直停止的事件
下面的事件必须终止在shadow边界附近
abort
error
select
change
load
reset
resize
scroll
selectstart
##事件分派
##样式
每个shadow root有一组样式表，称为shadow root样式表。这是个包含所有样式表的有序列表，与shadow root相关。
当一个shadow host有多个shadow roots时，css规则的级联顺序，定义在shadow root样式表的，以优先级升序排列与树栈中的顺序匹配，结束于最年轻的树。
执行边界封装，在一个封闭的树声明的css规则不能应用于shadow树，除非设置了apply-author-styles标志。这个标志标明了声明在封闭树的样式规则能够应用于shadow树。
即使设置了这个标志，选择器也不能跨越shadow边界。换句话说，设置了apply-author-styles，文档CSS规则只能整个匹配shadow树外或树内的。
为了级联顺序，shadow树的css规则设置了apply-author-style标志的，将会视为与shadow root相同作用范围的范围选择。在这样的背景下，如果A嵌套在B内，shadow树A和B，A的shadow root作为B的后代。
如果在一个嵌套树内的css规则以符合选择器结束，包含一个有效伪元素，该伪元素必须匹配该元素，与自定义伪元素相关联。
为确保下界封装，在shadow root样式表中声明的CSS规则一定不能应用在文档树中，除了以下两种情况。
+包含选择 ::distributed() 伪元素匹配封闭树中元素。
+@host @rule匹配嵌套树中的shadow host。
在包含shadow树的文档中，CSS属性从父节点继承，使用parent calculation 算法计算。这需要有以下：

