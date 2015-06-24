---
layout: post
title: "Git打补丁"
date: 2015-06-24 13:35:12 +0800
comments: true
categories: 其他
---
在平常生活中，经常遇到需要创建多个分支，多个分支可能需要一起维护，但很多时候是无法直接通过合并来进行维护的。例如：两个版本，一个是中国版，一个是国外版。两个版本可能基本功能是一样的，但是图片和界面都不一样，这时候如果我们是直接通过`merge`来进行同步的,可能做造成很多问题。所以如果有一个bug在两个版本都存在呢？如果你是一个程序员，那么你一定不会想同样的事情，两边都一遍，可能你觉得你不怕麻烦。好吧，那如果是10个版本呢?<!--more-->    

所以就需要打补丁了，我们把在一个版本的修改作为一个补丁，然后在其他版本都执行一遍。

###1. 获取修改信息

首先要给大家介绍`git`的一个命令:`git diff`，从命令上我们就能知道这是一个显示区别的命令。我们通过这个命令，可以将我们将我们的修改变成一个补丁，然后去其他的版本上。

    
1.git diff   

    这个命令没有加任何参数，所以显示出当前工作区与上一次提交的修改

```
RobinWu:diffDemo Robin$ git diff
diff --git a/test.txt b/test.txt
index 4871fd5..b78f83c 100644
--- a/test.txt
+++ b/test.txt
@@ -1 +1,2 @@
-test.txt
+
+kdfdftest.txt
```

2.giff diff pre_commit_id commit_id

    这个命令则是显示出两次提交之间的修改

```
RobinWu:diffDemo Robin$ git diff b7500087ca324b4b2f6497dd4cfe37cdbe07c7c2 b726a2381c06833c831ddaa6fcc2bb8f5ed4a883
diff --git a/test.txt b/test.txt
index b78f83c..4871fd5 100644
--- a/test.txt
+++ b/test.txt
@@ -1,2 +1 @@
-
-kdfdftest.txt
+test.txt
```

3.git diff branchName

    这个命令显示出当前分支与其他分支之间的改变

```
RobinWu:diffDemo Robin$ git branch newBranch
RobinWu:diffDemo Robin$ vim test2.txt
RobinWu:diffDemo Robin$ git add .
RobinWu:diffDemo Robin$ git commit -a -m "new file"
[master 4ee4ce9] new file
 1 file changed, 0 insertions(+), 0 deletions(-)
 create mode 100644 test2.txt
RobinWu:diffDemo Robin$ git diff newBranch
diff --git a/test2.txt b/test2.txt
new file mode 100644
index 0000000..e69de29
RobinWu:diffDemo Robin$ 
```

###2. 制作补丁
那么如果将这些改变制作成一个补丁呢？    
我们可以通过`>`定向符来制作一个补丁    
`git diff > my.patch`这个命令就会将修改信息保存到`my.patch`这个文件里。    

```
RobinWu:diffDemo Robin$ git diff b7500087ca324b4b2f6497dd4cfe37cdbe07c7c2 b726a2381c06833c831ddaa6fcc2bb8f5ed4a883 > my.patch
```

`my.patch`的文件内容如下
```
diff --git a/test.txt b/test.txt
index b78f83c..4871fd5 100644
--- a/test.txt
+++ b/test.txt
@@ -1,2 +1 @@
-
-kdfdftest.txt
+test.txt
~         
```

###3. 使用补丁
补丁做好了，那么如何使用这些补丁呢？
切换到对应的分支，然后执行`git apply my2.patch `就可以将修改应用到这个分支上了，但是请注意，这时候分支只是被修改了。    

我们执行`git status`

```
RobinWu:diffDemo Robin$ git status
On branch newBranch
Untracked files:
  (use "git add <file>..." to include in what will be committed)

    my.patch
    my2.patch
    test2.txt

nothing added to commit but untracked files present (use "git add" to track)
```

所以我还需要执行`git commit`才能将修改保存下来。    

###4. 格式化修改信息

`git format-patch -M `使用命令会将提交者的信息也保留下来，例如名字，邮箱地址等等。
```
RobinWu:diffDemo Robin$ git format-patch -M b7500087ca324b4b2f6497dd4cfe37cdbe07c7c2 b726a2381c06833c831ddaa6fcc2bb8f5ed4a883 
RobinWu:diffDemo Robin$ ls
0001-add-file.patch my.patch        my3.patch
0002-change-txt.patch   my2.patch       test2.txt
```

我们可以发现多了两个文件`0001-add-file.patch`和`0002-change-txt.patch`。他们的内容呢?

```
From b726a2381c06833c831ddaa6fcc2bb8f5ed4a883 Mon Sep 17 00:00:00 2001
From: RobinWu <lostskydev@gmail.com>
Date: Wed, 24 Jun 2015 22:30:37 +0800
Subject: [PATCH 1/2] add file

---
 test.txt | 1 +
 1 file changed, 1 insertion(+)
 create mode 100644 test.txt

diff --git a/test.txt b/test.txt
new file mode 100644
index 0000000..4871fd5
--- /dev/null
+++ b/test.txt
@@ -0,0 +1 @@
+test.txt
--
2.3.2 (Apple Git-55)
```

我们会发现补丁文件，包含了一些修改者的信息。这时我们合并过去，也可以将这些信息保留下来。

```
RobinWu:diffDemo Robin$ git am ../0001-add-new-file.patch 
Applying: add new file
RobinWu:diffDemo Robin$ git log
commit 5d21a53e295bbe79399d7118bf8304d03a264948
Author: RobinWu <lostskydev@gmail.com>
Date:   Wed Jun 24 23:16:22 2015 +0800

    add new file

commit 11f9071da62a9375db5ea616f86e621839dbb548
Author: RobinWu <lostskydev@gmail.com>
Date:   Wed Jun 24 23:15:51 2015 +0800

    add file
RobinWu:diffDemo Robin$ 
```
我们需要使用`git am patchFile`命令来进行合并，不然这些提交信息就无法保存下来。

