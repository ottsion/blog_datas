---
date: 2016-12-21T23:59:59+00:00
draft: false
author: "caicai"
title: "git的使用"
categories: ["杂记"]
tags: ["git"]
---

> 本篇主要说明git基础用法，随着我用，不断加吧

## 登陆账户
首先最好登陆账户，之后用来上传下载很方便了

```
git config --global user.name "your_username"
git config --global user.email your_email@domain.com
```

## 创建一个本地代码库
转到需要的地址，然后初始化环境，Git会在saveFile文件夹内创建一个名为.git的隐藏文件夹，那就是你的本地代码库。

```
cd d:\saveFile\
git init
```

## 加载修改文件
在仓库目录或者当前目录下，有全部加载和部分加载

```
git add .         //全部加载
git add my_file, my_other_file    //部分加载
```

## 提交文件
提交之前先写注释，修改文档的说明，然后查看当前状态，最后提交

```
git commit -m "initial commit"
git status
git push origin master
```

## 回滚到之前的提交状态
查看版本信息，然后选择ID迁出

```
git log
```

显示：

```
commit ca82a6dff817ec66f44342007202690a93763949Author: your_username your_email@domain.comDate:   Mon Nov 4 12:52:11 2013 -0700    changes the frontpage layout
commit 085bb3bcb608e1e8451d4b2432f8ecbe6306e7e7Author: your_username your_email@domain.comDate:   Mon Nov 4 11:40:33 2013 -0700    adds my new feature
commit a11bef06a3f659402fe7563abf99ad00de2209e6Author: your_username your_email@domain.comDate:   Mon Nov 4 10:37:28 2013 -0700    initial commit
```

最后选择版本执行：

```
git checkout 085bb3bcb
```




