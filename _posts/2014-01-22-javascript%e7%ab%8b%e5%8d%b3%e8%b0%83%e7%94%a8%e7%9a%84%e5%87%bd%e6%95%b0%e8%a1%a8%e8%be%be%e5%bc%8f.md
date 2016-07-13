---
id: 134
title: javascript立即调用的函数表达式
date: 2014-01-22T17:41:16+00:00
author: 李朝
layout: post
guid: http://www.lizhaoblog.com/?p=134
permalink: /archives/134
seo_follow:
  - 'false'
seo_noindex:
  - 'false'
categories:
  - javascript
  - web前端
tags:
  - javascript
  - web前端
  - 匿名函数
  - 立即调用的函数表达式
  - 自执行的匿名函数
---
jquery中最外围的一个方法即立即调用的函数表达式，代码如下：

<pre class="brush: jscript; title: ; notranslate" title="">(function(e,t){
//jquery code
})(window)
</pre>

代码一
  
这个方法开始接触时是一头雾水，这段代码其实等价于：

<pre class="brush: jscript; title: ; notranslate" title="">var fn = function(e,t){
//jquery code
}
fn(window);
</pre>

代码二
  
看到第二段代码便恍然大悟，其实代码一是因为第一对圆括的优先级最高关系，导致function(e,t){}先运行，即先声明了一个匿名函数，之后又碰到后面的一对圆括号：(window),使得之前声明的匿名函数得到立即执行。
  
除此之外，还有很多其他的匿名行数写法，通过使用一元或二元运算符来引导解析器，指明运算符附近是一个表达式。
  
如：

<pre class="brush: jscript; title: ; notranslate" title="">( function() {}() );
[ function() {}() ];
~ function() {}();
! function() {}();
+ function() {}();
- function() {}();

delete function() {}();
typeof function() {}();
void function() {}();
new function() {}();
new function() {};

var f = function() {}();

1, function() {}();
1 ^ function() {}();
1 &gt; function() {}();
// ...
</pre>