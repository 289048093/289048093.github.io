---
id: 120
title: Data URI scheme
date: 2014-01-20T18:06:58+00:00
author: 李朝
layout: post
guid: http://www.lizhaoblog.com/?p=120
permalink: /archives/120
seo_follow:
  - 'false'
seo_noindex:
  - 'false'
categories:
  - java
  - web前端
tags:
  - data uri scheme
  - java
  - php
  - web前端
---
这几天都在抢票 ，在研究360抢票插件时发现验证码的src为一串非正常URL的字符“data:image/png;base64,&#8230; &#8230;”，于是对这串字符感到好奇而进行了学习。
  
这串字符叫做：Data URI scheme。Data URI scheme是在RFC2397中定义的，目的是将一些小的数据，直接嵌入到网页中，从而不用再从外部文件载入。
  
字符组成：
  
data：表示取得数据的协定名称
  
image/png：表示MIME TYPE数据类型.
  
base64：编码方式。
  
逗号后面的是编码后的字符。
  
目前支持的类型有：

<pre class="brush: xml; title: ; notranslate" title="">data:,文本数据
data:text/plain,文本数据
data:text/html,HTML代码
data:text/html;base64,base64编码的HTML代码
data:text/css,CSS代码
data:text/css;base64,base64编码的CSS代码
data:text/javascript,Javascript代码
data:text/javascript;base64,base64编码的Javascript代码
data:image/gif;base64,base64编码的gif图片数据
data:image/png;base64,base64编码的png图片数据
data:image/jpeg;base64,base64编码的jpeg图片数据
data:image/x-icon;base64,base64编码的icon图片数据
</pre>

在php中base64用如下编码方法即可：

<pre class="brush: php; title: ; notranslate" title="">base64_encode(file_get_contents('test.png'))</pre>

在java中用sun.misc.BASE64Encoder进行base64编码文件：

<pre class="brush: java; title: ; notranslate" title="">File file = new File("test.png");
FileInputStream fis = new FileInputStream(file);
byte[] buf = new byte[1024];
int len = 0;
int count = 0;
byte[] total = new byte[]{};
while((len=fis.read(buf))!=-1){
	count = total.length+len;
	byte[] tmp = new byte[count];
	System.arraycopy(total, 0, tmp, 0, total.length);
	System.arraycopy(buf, 0, tmp, total.length ,len);
}
fis.close();
BASE64Encoder be = new BASE64Encoder();
String string = be.encode(total);
</pre>

使用data URI scheme与普通的http URI scheme区别如下：
  
1，浏览器对编码的数据不会进行缓存，并且随html同时加载，不另起请求；
  
2，便于传输，不会被拦截，只要页面能加载，便能获取到编码后的数据，这样360抢票插件就能将验证码图片进行编码后传输，在服务器后台进行识别，而不必去关心session问题；
  
3，保存数据的大小上受限制（Firefox 1.5支持高达100KB的数据）；
  
4，编码后会增加图片大小；
  
3，但如果使用过多会增加html的大小，可用css解决，即在css的background-image中使用data URI scheme，如：

<pre class="brush: css; title: ; notranslate" title="">background-image: url("data:image/png;base64/9j/4AA....+b0//2Q==");
</pre>

但是IE8以下版本不兼容。ie以下只得用http URI scheme.

参考：[http URI scheme与data URI scheme](http://blog.csdn.net/lingshaoxia/article/details/7673199)

[什么是data URI scheme及如何使用data URI scheme](http://sjolzy.cn/What-is-the-data-URI-scheme-and-how-to-use-the-data-URI-scheme.html "什么是data URI scheme及如何使用data URI scheme")