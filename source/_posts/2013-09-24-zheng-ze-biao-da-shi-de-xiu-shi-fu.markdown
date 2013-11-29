---
layout: post
title: "正则表达式的修饰符"
date: 2013-09-24 00:33:03 +0800
comments: true
categories: 
---
今天在看php的书籍时，生成这样一句正则表达式
 @^/(?P<page>([a-zA-Z_-]+?))/(?P<action>([a-zA-Z_-]+?))/(?P<id>([^/]+?))$@u\
 我一开始看不懂，后来查了资料才明白，这里面有几个重要的部分。
 1. 分割符
 2. 表达式
 3. 子模式 \r\n  4. 修饰符\r\n\r\n1.分隔符\r\n\r\n在这句表达式，有两个@符号，这两个就是分隔符，分割符中间位表达式，用于匹配字符串\r\n\r\n2.表达式\r\n\r\n用于匹配字符串\r\n\r\n3.子模式\r\n\r\n在php中使用`?P<name>`可以在使用这个preg_match()方法进行匹配时获取对应的键值对。\r\n\r\n例如:\r\n\r\n    /user-account/view/123\r\n\r\n这样的字符串将获取一个这样的数组\r\n\r\n    `[\"page\"]=> string(12) \"user-account\" [\"action\"]=> string(4) \"view\" [\"id\"]=> string(3) \"123\"\r\n\r\n4.修饰符\r\n\r\n在整个表达式的后面有一个u，是不是很奇怪，这个是修饰符，表示要开启某个模式。这个u表示是的utf8.