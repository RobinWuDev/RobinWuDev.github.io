---
layout: post
title: "博客系统优化及上线"
date: 2013-10-07 15:32:57 +0800
comments: true
categories: php
---
经过两天的处理，终于将博客系统1.0完成并且上线了，这次的博客系统采用MVC模式进行开发，对代码整理还有结构有了很多的规划，方便未来的扩展。

今天的任务的添加日志和性能测试。 <!-- more -->

1. 添加日志功能，将博客系统发生的错误信息保存到日志中，方便以后查看，解决问题。\

1. 性能测试，今天采用了在线测试方式进行性能测试，分别采用了[GTmetrix](http://gtmetrix.com/dashboard.html)、[pagespeed](http://developers.google.com/speed/pagespeed/insights/)、[LoadImpact](https://loadimpact.com)。一开始的测试结果直接导致数据库挂掉了，可见阿里云的服务器性能没那么高的，不过这些前两个测试平台有个好的地方在于他们会给出修改意见。基于测试报告我做服务器做了两个处理，一个gzip压缩，一个是浏览器缓存，做完之后，整体的浏览速度还是挺快的。第三个测试主要是测试多用户同事访问的，随着用户量的上涨，访问速度越来越慢，当50个用户的时候，达到了2分钟的时间，我个人觉得这个应该是访问数据库的问题，应该优化。

开启gzip压缩

修改apache配置文件,开启下面两个插件

    LoadModule deflate_module modules/mod_deflate.so
    LoadModule headers_module modules/mod_headers.so
    
并在.htaccess文件中添加这段代码
    
    <ifmodule mod_deflate.c>
        AddOutputFilter DEFLATE html xml php js css
    </ifmodule>
---
开启浏览器缓存

修改apache配置文件,开启下面两个插件
	
	LoadModule expires_module modules/mod_expires.so

并在.htaccess文件中添加这段代码

    <IfModule mod_expires.c>
        ExpiresActive On
        ExpiresDefault "access plus 12 month"
        ExpiresByType text/html "access plus 12 months"
        ExpiresByType text/css "access plus 12 months"
        ExpiresByType image/gif "access plus 12 months"
        ExpiresByType image/jpeg "access plus 12 months"
        ExpiresByType image/jpg "access plus 12 months"
        ExpiresByType image/png "access plus 12 months"
        EXpiresByType application/x-shockwave-flash "access plus 12 months"
        EXpiresByType application/x-javascript "access plus 12 months"
        ExpiresByType video/x-flv "access plus 12 months" </IfModule>