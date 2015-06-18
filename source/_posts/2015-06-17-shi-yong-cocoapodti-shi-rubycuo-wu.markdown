---
layout: post
title: "使用CocoaPod提示Ruby错误"
date: 2015-06-17 14:33:44 +0800
comments: true
categories: 其他
---
也不知道什么情况，最近在使用cocoapods总是会显示这些错误信息:

`/System/Library/Frameworks/Ruby.framework/Versions/2.0/usr/lib/ruby/2.0.0/universal-darwin14/rbconfig.rb:213: warning: Insecure world writable dir /Users/Robin/Qt in PATH, mode 040777`

<!--more-->
虽然不会影响使用，但是每次看到这些信息是很不爽的，所以就去看下问题，原来是权限问题，所以需要修改一下对应文件夹的权限。

`chmod 755 /Users/Robin/Qt`

搞定了。