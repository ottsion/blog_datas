---
date: 2017-06-17T23:59:59+00:00
draft: false
author: "caicai"
title: " KinectV2+Ubuntu 14.04+Ros 安装教程"
categories: ["ROS"]
tags: ["ros"]
---




## 前言

​     个人理解错误的地方还请不吝赐教，转载请标明出处，内容如有改动更新，请看原博：[http://www.cnblogs.com/hitcm/](http://www.cnblogs.com/hitcm/)

​     Kinect V2在Ubuntu下的开发问题，首先需要弄清楚的是你的设备是V1还是V2，这两个的驱动是不能通用的。

如下是V2（上）和V1（下）。看看自己的设备，然后再决定用哪个安装方案。

​     本文针对的是V2的情况。

[![image](http://images2015.cnblogs.com/blog/823608/201601/823608-20160110124614715-449864052.png)](http://images2015.cnblogs.com/blog/823608/201601/823608-20160110124612825-887589039.png)[![image](http://images2015.cnblogs.com/blog/823608/201601/823608-20160110124611746-558009212.png)](http://images2015.cnblogs.com/blog/823608/201601/823608-20160110124610418-448183572.png)

 

------

## 安装

1、首先git下载代码，很快下载好，放到~下面

`git clone https://github.com/OpenKinect/libfreenect2.git`

2、然后安装依赖项如下,最好事先编译安装好OpenCV

`sudo apt-get install build-essential cmake pkg-config libturbojpeg libjpeg-turbo8-dev mesa-common-dev freeglut3-dev libxrandr-dev libxi-dev`

3、然后安装libusb。此处需要添加一个PPA，就是下面的第一行命令，不然绝逼是装不上的。

```
sudo apt-add-repository ppa:floe/libusb
sudo apt-get update
sudo apt-get install libusb-1.0-0-dev
```



4、接着运行下面的命令，安装GLFW3

`sudo apt-get install libglfw3-dev`

如果没有成功的话，使用下面的命令，来代替上面的

```
cd libfreenect2/depends
sh install_ubuntu.sh
sudo dpkg -i libglfw3*_3.0.4-1_*.deb
```

5、然后安装OpenCL的支持库（不打算使用GPU，这一步直接跳过没做）

6、接着编译库

```
cd ..
mkdir build && cd build
cmake ..
make
sudo make install
```



------

## 测试

最后可以运行程序.

在build下面有个bin文件夹，放置生成的输出文件，插上kinect，然后运行。此时黄灯变成白色的，表示有驱动。注意：只能用于USB3的接口，好在台式机和笔记本都有3.0的口。

`./bin/Protonect`

但是提示权限不够，failed to open Kinect V2 Access denied

此时需要把libfreenect2文件夹下面的rules里面的一个90开头的文件复制到/etc/udev/rules.d/下面就可以了。

然后重新运行上面的命令就可以了。

------

## Ros接口安装

 

对于已经安装了Ros Indigo的Ubuntu14.04来说，使用下面的命令

```
cd ~/catkin_ws/src/
git clone https://github.com/code-iai/iai_kinect2.git
cd iai_kinect2
rosdep install -r --from-paths .
cd ~/catkin_ws
catkin_make -DCMAKE_BUILD_TYPE="Release"
rospack profile
```

　　

　　接下来可以测试了。

`roslaunch kinect2_bridge kinect2_bridge.launch`

　　然后重新开一个新的终端

`rosrun kinect2_viewer kinect2_viewer`

　　显示如下图所示。Good Luck and enjoy it!

　　![img](http://images2015.cnblogs.com/blog/823608/201601/823608-20160112220235116-1622259235.jpg)
