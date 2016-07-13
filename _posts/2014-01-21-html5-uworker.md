---
id: 124
title: Web Worker笔记
date: 2014-01-21T16:54:35+00:00
author: 李朝
layout: post
guid: http://www.lizhaoblog.com/?p=124
permalink: /archives/124
seo_follow:
  - 'false'
seo_noindex:
  - 'false'
categories:
  - HTML5
  - web前端
tags:
  - HTML5
  - javascript
  - Web Worker
---
由于javascript是单线程，为了模拟多线程，通常是利用setTimeOut，setInterval,ajax的`XMLHttpRequest。HTML5出现后，我们可以通过Worker实现js的多线程。`

代码：
  
main.html:

<pre class="brush: jscript; title: ; notranslate" title="">&lt;html&gt;
 &lt;head&gt;
  &lt;script type="text/javascript" src='test.js'&gt;&lt;/script&gt;
  &lt;script type="text/javascript"&gt;
	var worker = new Worker('test.js');
	worker.addEventListener('message',function(e){
		console.log('worker return :',e.data);
	},false);
	worker.postMessage('1');
	worker.postMessage('2');
	testFn();
	worker.postMessage('1');
  &lt;/script&gt;
 &lt;/head&gt;
&lt;/html&gt;</pre>

test.js:

<pre class="brush: jscript; title: ; notranslate" title="">self.addEventListener('message',function(e){
	switch (e.data)
	{
	case "1":
		self.postMessage(e.data+": var a="+self.a);
		self.a = "worker var a: 1";
	break;
	case "2":
		self.postMessage(e.data+": var a="+self.a);
		self.a = "worker var a: 2";
		break;
	}

},false);
function testFn(){
	console.log("this is worker.js function");
	self.a="worker var a : function";
}</pre>

ps:页面不能直接打开运行，需要放在web服务器上，否则报安全错误：Uncaught Error: SECURITY_ERR: DOM Exception 18
  
运行结果：

<pre class="brush: plain; title: ; notranslate" title="">this is worker.js function test.js:16
worker return : 1: var a=undefined main.html:8
worker return : 2: var a=worker var a: 1 main.html:8
worker return : 1: var a=worker var a: 2
</pre>

可以看到：
  
1，testFn后调用反而先运行了。
  
2，testFn将a赋值为&#8221;worker var a : function&#8221;，虽然先运行了，但是后面打印的结果均无此结果，

总结：postMessage是异步调用，worker对象的window与js的window并非共享的

ps:Worker传递的对象是通过复制传递的，并非共享的

参考：
  
[Web Workers 的基本信息](http://www.html5rocks.com/zh/tutorials/workers/basics/)