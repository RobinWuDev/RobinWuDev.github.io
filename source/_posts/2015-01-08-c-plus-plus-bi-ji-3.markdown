---
layout: post
title: "C++笔记(3)"
date: 2015-01-08 14:27:10 +0800
comments: true
categories: C++
---
弄个变量类别，C++能遇到的坑都比别人多
<!-- more -->
###类型别名
1. 还是那句话C++做什么事情都可以很多方法，连类型别名也是。    
`typedef int wage;``using wage = int`
2. 在使用类型别名进行声明，不要尝试用原定义替换，例如`typedef char *pstring;const pstring cstr=0;`,cstr是个指向char的常量指针。如果用原定义替换就是`const char *cstr`那么cstr就是一个指向`const char`的指针

###auto类型说明符
1. 使用`auto`可以让编译器来推算变量的类型，但前提是变量有初始值，我想这个功能类似于一些动态语言的类型推算，只是auto依赖的是编译器
2. 使用`auto`推算类型，会把`顶层const`忽略掉，所以如果要定义则需要手动添加`const auto`

###decltype类型指示符
1. decltype的功能跟上面的那个很像，但是差别在于不需要用表达式来初始化
2. decltype不会去对变量的类型进行处理，例如不忽略顶层const和引用
3. decltype使用变量，如果在变量上加括号得到的将是不一样的结果，因为加了括号，decltype就把变量认为是表达式，那么变量是一种可以作为赋值语句左值的特殊表达式，所以就会得到引用，例如`decltype(i)和decltype((i))`是不一样。

		int a = 3,b = 4;
		decltype(a) c = a;
		decltype((b)) d = a;
		++c;
		++d;

写这样的代码感觉就是自己跟自己玩捉迷藏，最后所有的变量都是4
部分文字来自[《C++ Primer》](http://www.amazon.cn/gp/product/B00ESUIL0O/ref=as_li_ss_tl?ie=UTF8&camp=536&creative=3132&creativeASIN=B00ESUIL0O&linkCode=as2&tag=robinwu-23)