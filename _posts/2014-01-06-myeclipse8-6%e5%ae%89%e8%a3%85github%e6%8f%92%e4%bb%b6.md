---
id: 90
title: myeclipse8.6安装github插件
date: 2014-01-06T22:03:56+00:00
author: 李朝
layout: post
guid: http://www.lizhaoblog.com/?p=21
permalink: /archives/90
categories:
  - eclipse
  - github
tags:
  - eclipse
  - github
---
安装egit：

1,下载egit，地址：[![](file:///C:/Users/Administrator/AppData/Roaming/Tencent/QQPinyin/Face/ImageCache/28.gif)![](file:///C:/Users/Administrator/AppData/Roaming/Tencent/QQPinyin/Face/ImageCache/28.gif)![](file:///C:/Users/Administrator/AppData/Roaming/Tencent/QQPinyin/Face/ImageCache/28.gif)http://pan.baidu.com/s/1bnrHZXH](http://pan.baidu.com/s/1bnrHZXH)

2,下载后解压到Genuitec\MyEclipse-8.6\dropins目录下，文件包括features、plugins，artifacts.jar、content.jar。

也可以通过link方式安装（未测试）

3,解压后重启myeclipse,打开window -> preferences ,在team下面出现了git表示安装成功。

4，配置git：打开window -> preferences -> general -> network Connections -> ssh2，选择key management选项卡，点击generate RSA key，在下面的文本框中会生成一串key，复制这一串key。

打开github页面，登录后进入设置界面（account settings），点击ssh keys菜单，进入页面后，点击add ssh key按钮，打开添加页面后，title可以任意填写，key的输入框输入刚才复制的那一串key，点击Add key添加完毕，权限配置完毕。

5，在github页面进入项目页面，找到：You can clone with HTTPS, SSH, or Subversion ,点击ssh，复制旁边的文本框中的地址。这个就是项目的checkout地址了

参考：[Eclipse下搭建GitHub开发环境](http://genuinecx.blog.51cto.com/2890523/1214687)