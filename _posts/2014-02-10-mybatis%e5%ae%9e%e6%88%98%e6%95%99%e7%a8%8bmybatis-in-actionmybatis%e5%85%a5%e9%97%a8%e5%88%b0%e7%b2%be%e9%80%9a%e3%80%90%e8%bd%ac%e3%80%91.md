---
id: 171
title: mybatis实战教程(mybatis in action),mybatis入门到精通【转】
date: 2014-02-10T16:11:53+00:00
author: 李朝
layout: post
guid: http://www.lizhaoblog.com/?p=171
permalink: /archives/171
seo_follow:
  - 'false'
seo_noindex:
  - 'false'
categories:
  - 未分类
---
**写在这个系列前面的话:**
  
以前曾经用过ibatis,这是mybatis的前身，当时在做项目时，感觉很不错，比hibernate灵活。性能也比hibernate好。而且也比较轻量级，因为当时在项目中，没来的及做很很多笔记。后来项目结束了，我也没写总结文档。已经过去好久了。但最近突然又对这个ORM 工具感兴趣。因为接下来自己的项目中很有可能采用这个ORM工具。所以在此重新温习了一下 mybatis, 因此就有了这个系列的 mybatis 教程.

**什么是mybatis**
  
MyBatis是支持普通SQL查询，存储过程和高级映射的优秀持久层框架。MyBatis消除了几乎所有的JDBC代码和参数的手工设置以及结果集的检索。MyBatis使用简单的XML或注解用于配置和原始映射，将接口和Java的POJOs（Plan Old Java Objects，普通的Java对象）映射成数据库中的记录.

**orm工具的基本思想**
  
无论是用过的hibernate,mybatis,你都可以法相他们有一个共同点：
  
1. 从配置文件(通常是XML配置文件中)得到 sessionfactory.
  
2. 由sessionfactory  产生 session
  
3. 在session 中完成对数据的增删改查和事务提交等.
  
4. 在用完之后关闭session 。
  
5. 在java 对象和 数据库之间有做mapping 的配置文件，也通常是xml 文件。

这是一个系列教程。我将把所有的链接都记录在此，以供参考.所有程序的 jar  包，可以在 第六个例子的程序中下载得到.

**<a href="http://www.yihaomen.com/article/java/303.htm" target="_blank" rel="external">mybatis实战教程(mybatis in action)之一：开发环境搭建</a>**

**<a href="http://www.yihaomen.com/article/java/304.htm" target="_blank" rel="external">mybatis实战教程(mybatis in action)之二：以接口的方式编程</a>**

**<a href="http://www.yihaomen.com/article/java/305.htm" target="_blank" rel="external">mybatis实战教程(mybatis in action)之三：实现单表的增删改查等操作</a>**

**<a href="http://www.yihaomen.com/article/java/306.htm" target="_blank" rel="external">mybatis实战教程(mybatis in action)之四:实现关联数据的查询</a>**

**<a href="http://www.yihaomen.com/article/java/309.htm" target="_blank" rel="external">mybatis实战教程(mybatis in action)之五:mybatis与spring3集成</a>**

**<a href="http://www.yihaomen.com/article/java/318.htm" target="_blank" rel="external">mybatis实战教程(mybatis in action)之六:mybatis与spring3 MVC 集成例子</a>**

**<a href="http://www.yihaomen.com/article/java/326.htm" target="_blank" rel="external">mybatis实战教程(mybatis in action)之七:实现mybatis分页(源码下载)</a>**

**<a href="http://www.yihaomen.com/article/java/328.htm" target="_blank" rel="external">mybatis实战教程(mybatis in action)之八:mybatis 动态 sql 语句基础</a>**

**<a href="http://www.yihaomen.com/article/java/331.htm" target="_blank" rel="external">mybatis实战教程(mybatis in action)之九:mybatis 代码生成工具的使用</a>**

**<a href="http://www.yihaomen.com/article/java/336.htm" target="_blank" rel="external">mybatis实战教程(mybatis in action)之十:mybatis SqlSessionSupport 的使用,构件DAO 层的应用</a>**

&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8211;补充教程&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;-
  
**<a href="http://www.yihaomen.com/article/java/425.htm" target="_blank" rel="external">mybatis补充教程之一：在控制台显示sql语句，类似hibernate show_sql.</a>**

**<a href="http://www.yihaomen.com/article/java/426.htm" target="_blank" rel="external">mybatis补充教程之二：传递多个参数的方法(Multiple parameters)</a>**

**<a href="http://www.yihaomen.com/article/java/428.htm" target="_blank" rel="external">mybatis补充教程之三：缓存的使用</a>**

原文链接：<http://www.yihaomen.com/article/java/302.htm>