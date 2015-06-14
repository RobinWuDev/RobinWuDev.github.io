---
layout: post
title: "iOS设计模式-原型模式"
date: 2013-12-24 16:27:00 +0800
comments: true
categories: 设计模式
---

**原型模式:** 使用原型实例创建对象的种类，并通过复制这个原型创建新的对象。 --最早定义于[《设计模式》](http://www.amazon.cn/gp/product/B001130JN8/ref=as_li_ss_tl?ie=UTF8&camp=536&creative=3132&creativeASIN=B001130JN8&linkCode=as2&tag=robinwu-23)

<!-- more -->
上面是原型模式的的定义，在我看来原型模式就是复制模式，根据一个已有的实例复制出另外一个实例，被复制的那个实例就原型，所以才被叫做原型模式。

下面主要讲在iOS里面怎么实现原型模式。在iOS里面本身就自带了复制功能，有浅复制，也有深复制。浅复制是复制指针，而深复制是复制内容。所以我认为深复制才能真的算是原型模式。因为深复制完，两个实例是完全独立的个体，只是内容一样，这样才符合原型的功能。

现在我就直接上代码了，怎么实现深复制。

首先iOS协议里面有个*NSCoping*协议，想让创建的实例支持深复制，该实例的类需要实现这个协议，并实现该协议的```- (id)copyWithZone:(NSZone *)zone```方法。

``` objective-c

#import <Foundation/Foundation.h>

@interface DDCommand : NSObject<NSCopying>

@property (copy, nonatomic) NSString *text;
@end

```

``` objective-c

#import "DDCommand.h"

@implementation DDCommand

- (id)copyWithZone:(NSZone *)zone {
    DDCommand *command = [[[self class]allocWithZone:zone]init];
    command.text = _text;
    return command;
}

@end

```

DDCommand类已经具有深复制的功能了，我们下面开始使用。

``` objective-c

	 DDCommand *command1 = [[DDCommand alloc]init];
    command1.text = @"command1";
    DDCommand *command2 = [command1 copy];
    command2.text = @"command2";
    
```
<a href="/blogFiles/download/20131224.zip">demo</a>
