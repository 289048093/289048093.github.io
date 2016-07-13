---
id: 139
title: IntelliJ Idea使用记录
date: 2014-02-07T22:05:15+00:00
author: 李朝
layout: post
guid: http://www.lizhaoblog.com/?p=139
permalink: /archives/139
seo_follow:
  - 'false'
seo_noindex:
  - 'false'
categories:
  - ItelliJ Idea
tags:
  - ItelliJ Idea
---
初次使用Idea,比起以前使用的eclipse，有很大的不习惯。
  
首先有很多概念上的区别。如：

<table style="border: 1px solid black;" border="1">
  <tr>
    <td style="border: 1px solid black;" width="100px">
    </td>
    
    <td style="border: 1px solid black;" width="100px">
      eclipse
    </td>
    
    <td style="border: 1px solid black;" width="100px">
      idea
    </td>
  </tr>
  
  <tr>
    <td style="border: 1px solid black;">
      工作空间
    </td>
    
    <td style="border: 1px solid black;">
      workspace
    </td>
    
    <td style="border: 1px solid black;">
      project
    </td>
  </tr>
  
  <tr>
    <td style="border: 1px solid black;">
      项目
    </td>
    
    <td style="border: 1px solid black;">
      project
    </td>
    
    <td style="border: 1px solid black;">
      Module
    </td>
  </tr>
</table>

其次，使用上的区别：
  
**打开文件与路径关联**：eclipse下是按下![QQ图片20140207221022](http://www.lizhaoblog.com/wp-content/uploads/2014/02/QQ图片20140207221022.jpg)按钮即可。
  
在idea下可以按下![QQ截图20140207221203](http://www.lizhaoblog.com/wp-content/uploads/2014/02/QQ截图20140207221203.jpg)来进行关联，但是这仅仅是单次的，并不会在下次打开文件的时候自动去关联，可以通过勾选![QQ图片20140207225740](http://www.lizhaoblog.com/wp-content/uploads/2014/02/QQ图片20140207225740.jpg)中的Autoscroll to Source和Autoscroll from Source来实现。
  
**显示行号**：这里你可以右键文件编辑窗口右侧，选择“show line Numbers”来实现，但是这同样是单次有效，要实现全局有效，可在File&#8211;>Settings&#8211;>Editor&#8211;>Appearance勾选“Show line numbers”来实现。![QQ图片20140207230642](http://www.lizhaoblog.com/wp-content/uploads/2014/02/QQ图片20140207230642.jpg)

**像eclipse中一样自动编译部署**：默认jsp，html等文件修改后，都不会自动部署，在tomcat设置窗口中设置下图选项（on &#8216;update&#8217; action,on frame deactivation）：<img class="alignnone size-full wp-image-163" alt="QQ截图20140210143043" src="http://www.lizhaoblog.com/wp-content/uploads/2014/02/QQ截图20140210143043.jpg" width="352" height="75" />,这样修改文件保存以后，便会自动编译部署，可以马上看到修改的效果了