---
layout: post
title: "MAC OS X下制作Centos启动盘"
date: 2015-02-14 19:19:18 +0800
comments: true
categories: 其他
---
越来越发现自己老了，记忆力不行了，所以只能将一些东西记录下来了，省的下一次又要再去网上找,
搞了一台二手电脑，打算装一个centos来做是一些事情，所以制作了一个USB的启动盘
<!-- more -->

1.使用Disk Util 格式化USB驱动盘
![](/blogFiles/images/2015/02/14/step1.png)
![](/blogFiles/images/2015/02/14/step2.png)
2.将ISO文件转化为IMG文件
    
    hdiutil convert -format UDRW -o CentOS-7.0-1406-x86_64-DVD.dmg CentOS-7.0-1406-x86_64-DVD.iso

3.获取USB盘设备号

    diskutil list

4.卸载USB盘  

    diskutil unmountDisk /dev/disk2

5.开始创建启动盘

    sudo dd if=CentOS-7.0-1406-x86_64-DVD.dmg of=/dev/disk2 bs=1m
