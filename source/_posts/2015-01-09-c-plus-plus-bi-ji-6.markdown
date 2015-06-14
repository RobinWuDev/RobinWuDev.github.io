---
layout: post
title: "C++笔记(6)"
date: 2015-01-09 21:02:57 +0800
comments: true
categories: C++
---
这两章都是跟其他语言差不多的，除了显式转换，不过那个也可以用其他语言转换的方式，只是比较危险，不过一般用的时候注意下就行
<!--more-->
###表达式
1. 当运算符作用于类类型的运算对象时，用户可以自行定义其含义。这种做法被成为重载运算符，在重载运算符中，运算符的类型和返回值的类型可以自定义，但是运算对象、优先级、和结合律是无法改变的。
2. 当一个对象被用作右值的时候，用的是对象的值（内容），当对象被用作左值的时候，用的是对象的身份（在内存中的位置）
3. 使用decltype的时候，如果表达式的求值结果是左值，decltype将得到一个引用类型，记住是表达式
4. 其他的运算符没什么吐槽的，就那些，不过`sizeof`是一个很少见的运算符，他返回一条表达式或一个类型名字所占的字节数,对于数组来说，他会返回整个数组所占空间的大小，对于string和vector则会返回该类型固定部分的大小

###显式转换
1. 一个命名的强制类型转换具有如下形式:`const-name<type>(expression)`
2. const-name包括了`static_cast`,`dynamic_cast`,`const_cast`和`reinterpret_cast`
3. `static_cast`用于对任何具有明确定义的类型转换，只要不包含底层const。例如将`double slope=static_cast<double>(j)`
4. `const_cast`只能用于对底层const的转换，书上说如果对象是一个常量，再使用`const_cast`执行写操作就会产生未定义的后果，我测试下，如果是不是常量可以正常修改，不是常量，改了没用，感觉是在转换过程中创建了一个副本
5. `reinterpret_cast`是进行较低层次的转换，转了之后也不一定能用，所以还是少用这个

###语句
语句这部分也没啥好看，我也是略过去，基本上跟其他语言没啥区别

###异常
1. 我发现C++里面似乎没有finally这样的东西，那么怎么在异常发生时，将一些东西关闭掉呢?
2. `exception`头文件定义最懂用的异常类`exception`
3. `stdexcept`头文件定义了几种常用的异常类
4. `new`头文件定义了`bad_alloc`异常类型
5. `type_info`定义了`bad_cast`异常类型
	
	1. exception 最常见的问题
	2. runtime_error 只有在运行时才能检测出的问题
	3. range_error 运行时错误: 生成的结果超出了有意义的值域范围
	4. overflow_error 运行时错误: 计算上溢
	5. underflow_error 运行时错误: 计算下溢
	6. logic_error 程序逻辑错误
	7. domain_error 逻辑错误: 参数对应的结果值不存在
	8. invalid_argument 逻辑错误: 无效参数
	9. length_error 逻辑错误: 试图创建一个超出该类型最大长度的对象
	10. out_of_range 逻辑错误: 使用一个超出有效范围的值


部分文字来自[《C++ Primer》](http://www.amazon.cn/gp/product/B00ESUIL0O/ref=as_li_ss_tl?ie=UTF8&camp=536&creative=3132&creativeASIN=B00ESUIL0O&linkCode=as2&tag=robinwu-23)
