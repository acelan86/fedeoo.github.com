---
layout: post
title: "使用jekyll构建博客"
description: ""
category: bulid-up
tags: [gitblog jekyll]
---
{% include JB/setup %}
因为需要在github下记录一些东西，git上使用的git wiki，不是很顺手。网上有推荐wordpress或用github自己搭建blog的。

以下记录了今天折腾的过程。在windows下搭建。
由于刚接触github，git的操作还不够顺溜，又多费了把劲。(由于对git不熟，在使用该命令时，按照网上的git clone git@github.com:fedeoo/***.git 老是出错。)

<http://www.cnblogs.com/purediy/archive/2013/03/07/2948892.html>介绍了怎么开始。

安装ruby就不说了。在window下安装ruby的包真是坑。
gem install jekyll 就是不成功，找不到库。
之前安装sass时,也遇到过这问题,就从github上下载之后,本地安装搞定。

	git clone https://github.com/*****/**.git localpath
	gem build **.gemspec 生成**.gem文件 
	gem install --local **.gem

不过这次想死的是jekyll还需要依赖其它的一些库，十几个啊！让人绝望的是网上一精神可嘉的哥们手动下载安装之后，居然还没成功。还好，在网上找到了替代的资源。

	gem sources --remove http://rubygems.org/ 
	gem sources -a http://ruby.taobao.org/ 
	gem install jekyll

安装jekyll之后。下载bootstrap,算是一个初始的项目了。
下面的介绍已经很详细了，不再多说。

<http://jekyllbootstrap.com/usage/jekyll-theming.html>
<http://www.mceiba.com/develop/jekyll-introduction.html>(中文)

本以为可以了，又来了个坑爹的问题---中文问题。

博文的编码是utf-8,按照以上的解决方案，改变控制台编码，不起作用。

运行jekyll –server时出现的编码问题：invalid byte sequence in GBK ...."

找到ruby目录下gems/jekyll-0.11.0/lib/jekyll/convertible.rb 31行修改为下面的内容

	self.content = File.read(File.join(base, name), :encoding => "utf-8")
