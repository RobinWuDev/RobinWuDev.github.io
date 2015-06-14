---
layout: post
title: "C++笔记(4)"
date: 2015-01-08 15:43:20 +0800
comments: true
categories: C++
---
这节没啥好说的。自定义数据结构和文件保护符
<!--more-->
###自定义数据结构
1. 类体后面必须添加分号，原因就是可以在类体后面添加变量明，我觉得这就是从C语言里面留下来的。
2. C++11新标准规定，会为数据成员提供一个类内初始值（听这个名字就奇葩），用于初始化数据成员。

这一节确实没啥好说，可能是因为我对类的使用比较了解，毕竟也是学编程也快5年了。不过如果说C++是一种面向对象语言，有点不靠谱，应该说C++可以做面对对象语言，也可以做其他语言。他支持的东西太多了，导致学起来各种恶心。

###头文件保护符
		#ifndef TEST_H
		#define TEST_H
		#include <string>
		
		struct Sales_data {
		    std::string bookNO;
		    unsigned units_sold = 0;
		    double revenue = 0.0;
		};
		#endif

看了这个能能知道OC里面,#import的作用了，类似的
部分文字来自[《C++ Primer》](http://www.amazon.cn/gp/product/B00ESUIL0O/ref=as_li_ss_tl?ie=UTF8&camp=536&creative=3132&creativeASIN=B00ESUIL0O&linkCode=as2&tag=robinwu-23)