---
id: 20
title: URLConnection
date: 2013-12-26T21:52:54+00:00
author: 李朝
layout: post
guid: http://www.lizhaoblog.com/?p=20
permalink: /archives/20
categories:
  - java
tags:
  - java
  - URLConnection
---
<p align="left">
  代码：
</p>

<pre class="brush: java; title: ; notranslate" title=""> HttpURLConnection conn = null;
  InputStream is = null;
  OutputStream os = null;
  try {
   conn = (HttpURLConnection) new URL(url).openConnection();
   is = conn.getInputStream();
 // code
  } catch (MalformedURLException e) {
  } catch (IOException e) {
  } finally {
   try {
     if (conn != null)
       conn.disconnect();
     if (is != null)
      is.close();
   } catch (IOException e) {
   }
  }
 </pre>

<p align="left">
  在linux下，当连接数多的时候会发现有很多CLOSE_WAIT的连接。windows下好像会好些。
</p>

<p align="left">
  这种情况是因为：在http1.1中所有的conntection默认都是persist Connection（keep-alive）。当Connection.getInputStream() close后，HTTP protocol handler会试图去清理Connection，成功的话会将conntection放入connection缓存，以便下次能重复利用。
</p>

<p align="left">
  修改默认keep-alive:
</p>

<pre class="brush: java; title: ; notranslate" title="">System.setProperty("http.keepAlive", "false");</pre>

<p align="left">
  修改connection缓存机制：
</p>

<pre class="brush: java; title: ; notranslate" title="">connection.setUseCaches(false);</pre>

<p align="left">
  参考：<a href="http://docs.oracle.com/javase/6/docs/technotes/guides/net/http-keepalive.html">Persistent Connections</a>
</p>

<p align="left">
  <a href="http://bugs.sun.com/bugdatabase/view_bug.do?bug_id=6367268">http://bugs.sun.com/bugdatabase/view_bug.do?bug_id=6367268</a>
</p>

<p align="left">
  http://stackoverflow.com/questions/4767553/safe-use-of-httpurlconnection
</p>

<p align="left">
  http://hea-www.harvard.edu/~fine/Tech/addrinuse.html
</p>