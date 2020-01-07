---
date: 2017-06-22T23:59:59+00:00
draft: false
author: "caicai"
title: " 先锋机器人在ROS上的安装"
categories: ["ROS"]
tags: ["ros, pioneer"]
---



### 一. 安装Ubuntu###

第一步首先需要给先锋机器人配置Ubuntu环境，这里选用Ubuntu14.04版本进行安装，安装步骤一般为：

1. 制作Ubuntu14.04的U盘启动器
2. 进入先锋机器人BIOS界面设置U盘启动，这里我试了F2、Esc、F10、F12。。。。都没起作用，最后找了个带$F_n$ 的键盘，一通$F_n+Esc+shift+ctrl+Del+F12$  结果进入了。。。具体不清楚哪个起作用了。
3. 按照一般双系统做法进行了系统安装，记得选磁盘一定要选择other那个，别直接默认结果格式化了已有的磁盘。

[官方](http://robots.mobilerobots.com/wiki/Reinstalling_Linux)

### 二.安装ROS###

安装原文：[点击进入](http://wiki.ros.org/indigo/Installation/Ubuntu)

1. Setup your sources.list
  Setup your computer to accept software from packages.ros.org.

```
sudo sh -c 'echo "deb http://packages.ros.org/ros/ubuntu $(lsb_release -sc) main" > /etc/apt/sources.list.d/ros-latest.list'
```

2. Set up your keys

```
sudo apt-key adv --keyserver hkp://ha.pool.sks-keyservers.net:80 --recv-key 421C365BD9FF1F717815A3895523BAEEB01FA116
```

3. Installation

````
sudo apt-get update
sudo apt-get install ros-indigo-desktop-full
apt-cache search ros-indigo
sudo rosdep init
rosdep update
echo "source /opt/ros/indigo/setup.bash" >> ~/.bashrc
source ~/.bashrc
sudo apt-get install python-rosinstall
````

###三.安装ROSARIA###

安装原文：[官方信息](http://wiki.ros.org/ROSARIA/Tutorials/How%20to%20use%20ROSARIA)
```
cd ~/catkin_ws/src
git clone https://github.com/amor-ros-pkg/rosaria.git
```
下载驱动部分：[地址](http://robots.mobilerobots.com/wiki/Aria) ，得到libaria_2.9.1+ubuntu12_amd64.deb。

安装：
`sudo dpkg -i libaria_2.9.1+ubuntu12_amd64.deb`	
更新：
```
sudo apt-get update
sudo apt-get upgrade
```
自动安装依赖：
```
rosdep update
rosdep install rosaria
```

最后编译一下：

````
catkin_make
````

运行：
```
rosrun rosaria RosAria _port:=localhost:8101

rostopic pub -1 /RosAria/cmd_vel geometry_msgs/Twist '[0.1, 0.0, 0.0]' '[0.0, 0.0, 0.0]'
```



### 四.安装激光及视频驱动###

