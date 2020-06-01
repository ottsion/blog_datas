---
title: "Pycharm中远程上传项目失败"
date: 2020-05-26T16:58:24+08:00
draft: false
author: "caicai"
tags: ["pycharm"]
categories: ["bugs"]
---





问题：

使用pycharm远程开发，新建了一个项目去连接远端python解释器，发现No files or folders found to process

```
[2020/2/17 21:14] No files or folders found to process
[2020/2/17 21:15] Upload to user1@10.xxx.xxx.xxx:22 (2)

[2020/2/17 21:26] Download from xxx
[2020/2/17 21:26] No files or folders found to process
```





解决方法：

进入：tools - deployment - configuration

左侧选择对应的远端python，右侧Connection中Web server URL填写http:///， 一般出问题是因为这里默认http://

重新upload即可