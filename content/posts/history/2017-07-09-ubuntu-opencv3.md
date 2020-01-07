---
date: 2017-07-09T23:59:59+00:00
draft: false
author: "caicai"
title: "ubuntu14.04下安装opencv3.2.0"
categories: ["常用库"]
tags: ["opencv,图像处理"] 
---



一、下载 [OpenCV](http://lib.csdn.net/base/opencv) 3.2：

​        网址：[http://opencv.org/releases.html](http://opencv.org/releases.html)

二、安装常用依赖项：

```
sudo apt-get install build-essential libgtk2.0-dev libvtk5-dev libjpeg-dev libtiff4-dev libjasper-dev libopenexr-dev libtbb-dev  
```

三、安装 [opencv](http://lib.csdn.net/base/opencv) 3.2：

1. 建立编译文件目录,并切换到该目录下：            
```
mkdir build  
cd build  
```
2. 开始编译(.. 为上层目录，不可缺少)：

```
cmake ..
make
```

​       如果遇到这样的错误：-- ICV: Downloading ippicv_linux_20151201.tgz... CMake Error at 3rdparty/ippicv/downloader.cmake:73 (file): file DOWNLOAD HASH mismatch

​             去这里（[点击打开链接](http://download.csdn.net/detail/u013831198/9845694)）下载ippicv_linux_20151201.tgz 并粘贴（替换）到目录opencv-3.2.0/3rdparty/ippicv/downloads/[Linux](http://lib.csdn.net/base/linux)-808b791a6eac9ed78d32a7666804320e/

3. make -j4 (-j4表示开启4个线程编译，取决于CPU的速度，比如我make -j7)

```
make j4
```

4. 安装：默认安装到 /usr/local 下
```
sudo make install   
```
