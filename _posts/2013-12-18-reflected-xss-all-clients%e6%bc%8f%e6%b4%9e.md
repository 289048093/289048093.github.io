---
id: 76
title: Reflected XSS All Clients漏洞
date: 2013-12-18T22:50:45+00:00
author: 李朝
layout: post
guid: http://www.lizhaoblog.com/?p=19
permalink: /archives/76
categories:
  - web前端
tags:
  - css
  - html
  - javascript
  - web前端
---
XSS的全称是Cross Site Scripting,跨站脚本

这第一个单词是Cross，为什么缩写成X呢？因为CSS是层叠样式表的缩写（Cascading Style Sheets）的缩写，同时Cross发音和X相似，为了避免混淆用X来代替,缩写成XSS。

XSS的原理就是往HTML中注入脚本.

三种方式：

1，直接注入

<pre class="brush: xml; title: ; notranslate" title="">&lt;script&gt;

//code

&lt;/script&gt;
</pre>

2,通过img,a等支持的src属性的标签注入：

&nbsp;

<pre class="brush: xml; title: ; notranslate" title="">&lt;img src="javascript://code"/&gt;</pre>

3，通过dom的事件注入：

&nbsp;

<pre class="brush: xml; title: ; notranslate" title="">&lt;img src="errorsrc" onerror="//code"/&gt;</pre>

转码注入：

&nbsp;

1,HTML编码可以用&#ASCII方式来写，这种XSS转码支持10进制和16进制，SQL注入转码是将16进制字符串赋给一个变量，而XSS转码则是针对属性所赋的值

<pre class="brush: xml; title: ; notranslate" title="">&lt;img src="javascript:alert('xss')"/&gt;</pre>

转义后：

&nbsp;

<pre class="brush: xml; title: ; notranslate" title="">&lt;img src="&amp;#106&amp;#97&amp;#118&amp;#97&amp;#115&amp;#99&amp;#114&amp;#105&amp;#112&amp;#116&amp;#58&amp;#97&amp;#108&amp;#101&amp;#114&amp;#116&amp;#40&amp;#39&amp;#104&amp;#101&amp;#108&amp;#108&amp;#111&amp;#32&amp;#119&amp;#111&amp;#114&amp;#100&amp;#39&amp;#41"/&gt;</pre>

这个&#分隔符还可以继续加0变成“&#0106” ，“&#00106” ，“&#000106”　，“&#0000106”等形式。

&nbsp;

2,我们的JavaScript中的”\”字符是转义字符，所以可以使用”\”连接16进制字符串运行代码,样式表也支持分析和解释”\”连接的16进制字符串形式，浏览器能正常解释:

<pre class="brush: css; title: ; notranslate" title="">&lt;style&gt;
BODY { background: url("javascript:alert('xss'))") }
&lt;/style&gt;
</pre>

转移后：

&nbsp;

<pre class="brush: css; title: ; notranslate" title="">&lt;style&gt;
BODY { background: \75\72\6c\28\68\74\74\70\3a\2f\2f\31\32\37\2e\30\2e\30\2e\31\2f\78\73\73\2e\67\69\66\29 }
&lt;/style&gt;
</pre>

参考：[浅析XSS(Cross Site Script)漏洞原理](http://publish.it168.com/2007/0704/20070704004201.shtml)

&nbsp;