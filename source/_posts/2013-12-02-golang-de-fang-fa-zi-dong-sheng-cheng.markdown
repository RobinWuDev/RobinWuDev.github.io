---
layout: post
title: "golang的方法自动生成"
date: 2013-12-02 21:05:38 +0800
comments: true
categories: go
---
Golang方法的自动转换

在go语言中会自动根据下面这个方法  
``` go 
func (a Integer) Less(b Integer) bool
```  
自动生成下面的这个方法  
``` go
func (a *Integer) Less(b Integer) bool
```  <!-- more -->

所以如果有一个接口定义为这样  
``` go  
type LessAdder interface {
	Less(b Integer) bool
}
```  

而类型定义为这样  
``` go
type Integer int

func (a Integer) Less(b Integer) bool {
	return a < b
}
```
就可以直接说Integer实现了接口LessAdder

可以直接赋值  
``` go
 var a Integer = 1
  var b LessAdder = a
```