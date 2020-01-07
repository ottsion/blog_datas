---
date: 2017-06-24T23:59:59+00:00
draft: false
author: "caicai"
title: "先锋机器人使用手册"
categories: ["ROS"]
tags: ["ROS, pioneer"]
---

# 先锋机器人使用手册

## 前期准备

我们这里说的环境是`Ubuntu14.04`，`ROS-indigo`版本，将`pioneer`机器人装入`Ubuntu`系统后用笔记本电脑实现控制。详细情况可以参考： [ROS](http://wiki.ros.org)和[pioneer ROS](http://www.mobilerobots.com/ResearchRobots/P3AT.aspx) 。

## 软件安装

### 安装Ubuntu

1.制作`Ubuntu14.04`的U盘启动器后，记得找个含有$F_n$的键盘，使用快捷键进入机器人`bios`系统选择U盘启动。
2. 如果是双系统记得安装过程中选择硬盘时为`something else` ，自己划分各盘容量。
3. 选择实验室wifi链接，方便后期直接笔记本控制。

### 安装ROS

这里选用 `indigo`版本的ROS操作系，下面链接为官方安装地址，按照操作走完就可以了：[官方地址](http://wiki.ros.org/indigo/Installation/Ubuntu)

安装完毕后强烈建议学习初步教程，大概是十几篇简短文章，学完后掌握基本ROS下开发方法，了解ROS构成：[Tutorials](http://wiki.ros.org/ROS/Tutorials)

### 安装ROSARIA

[ROSARIA]( http://wiki.ros.org/ROSARIA ) 提供了一个移动机器人与ROS平台的接口，通过它你可以实现与机器人的互动，所以第一步学习就是要安装好它。

- 其中需要在[pioneer 官网](http://robots.mobilerobots.com/wiki/Aria) 安装` ARIA 2.9.1-Ubuntu 12.04.2 (precise) or later, amd 64-bit architecture` 千万不要忘记了，这是驱动的底层实现，没有这个只安装`ROSARIA`是不行的。这个选择`sudo dpkg -i ` 安装方式。最终文件安装在`/usr/local/`下面。

具体安装步骤可以参考：[这里](http://wiki.ros.org/ROSARIA/Tutorials/How%20to%20use%20ROSARIA) 

```
1) 创建工作空间，如果有了直接走 2).
$./opt/ros/indigo/setup.bash
$mkdir-p~/catkin_ws/src
$cd~/catkin_ws/src
$catkin_init_workspace
$cd~/catkin_ws
$catkin_make
$cd~/catkin_ws/src
2) 安装rosaria包:
$gitclonehttps://github.com/amor-ros-pkg/rosaria.git
$source~/catkin_ws/devel/setup.bash
$echo"source~/catkin_ws/devel/setup.bash">>~/.bashrc
$source~/.bashrc
%rosdep可以安装额外需要的工具包
$rosdepupdate
$rosdepinstallrosaria
$cd~/catkin_ws/
$catkin_make
```

到此为止你应该安装好了两样东西：
1)  `Aria library` 应该在` /usr/local/` 目录下。
2)   `/catkin_ws/src/rosaria` 已经编译到位。

## 电脑间通信

这里以我的电脑为例：

|         | 我的笔记本            | 机器人pioneer       |
| ------- | ---------------- | ---------------- |
| IP      | 192.168.1.102    | 192.168.1.10     |
| OS      | Ubuntu 14.04 LTS | Ubuntu 14.04 LTS |
| ROS     | ROS indigo       | ROS indigo       |
| RosAria | installed        | installed        |

1. 被控端pioneer机器人安装ssh用来远程连接:`sudo apt-get install openssh-client`以及`sudo apt-get install openssh-server`

2. 修改两台电脑的hosts文件：

   `ip`地址可以通过`ifconfig` 查看到。

   首先是笔记本电脑：`sudo gedit  /ets/hosts`

   ```
   127.0.0.1	localhost
   127.0.1.1	ottsion     //这里是因为我的主机名就是ottsion,你可以用hostname查看自己主机名

   192.168.1.102   ottsion
   192.168.1.10    pioneer
   ```

   再修改pioneer机器人的`hosts`文件：

   ```
   127.0.0.1       localhost
   127.0.1.1       pioneer   //这里在安装时我填的主机名pioneer，你也可以查看机器人hostname确定

   192.168.1.10    pioneer
   192.168.1.102   ottsion
   ```

3. 如此这般以后你就可以通信了，比如：

   笔记本上远程连接pioneer机器人：`ssh robot@pioneer` (注：robot为我的用户名，pioneer为主机名，主机名你也可以用ip地址替代)

   输入密码即进入了pioneer机器人。

4. 注意，因为两台电脑用ROS通信其中一个需要启动`roscore`为主，我选用了我的电脑，需要对两台电脑的`.bashrc`文件做一下修改以便后期不用每次输入`ROS_MASTER_URI` ：

   我的电脑：`gedit .bashrc`:

   ```
   export ROS_HOSTNAME=ottsion
   export ROS_MASTER_URI=http://ottsion:11311
   ```

   pioneer机器人：

   ```
   export ROS_HOSTNAME=pioneer
   export ROS_MASTER_URI=http://ottsion:11311
   ```

## 控制运动

此处需要两台电脑一起操作了：

|      | 我的笔记本         | 机器人pioneer                               |
| ---- | ------------- | :--------------------------------------- |
| IP   | 192.168.1.102 | 192.168.1.10                             |
|指令步骤|(1)$roscore|(2)rosrunrosariaROSAria_port:=/dev/ttyS0|

**注意：** 

1. 如果 `ROSAria`节点启动不起来很可能是因为权限问题，输入：`sudo chmod a+rw /dev/ttyS0` 应该可以解决（这是pioneer机器人下）。
2. 如果你想单纯在笔记本端操作，可以直接`ssh`方式连接到pioneer机器人操作。

这里给出一个演示运动程序`rob_key`： `rosrun rosaria rob_key` ,可以用键盘方向键控制实体机器人运动了（`rob_key`非自带程序，可自写，发布`geometry_msgs/Twist.h`消息在`/RosAria/cmd_vel`主题即可）。



## MobileSIm和gazebo仿真

### MobileSim

[MobileSim](http://robots.mobilerobots.com/wiki/MobileSim) 是pioneer官网提供的版本，可以在这里面进行仿真学习，地图有预置的(目录`usr/local/MobileSIm`)，也可以自定义。

下载地址：[这里](http://robots.mobilerobots.com/wiki/MobileSim) 。安装后可执行测试：

1. 启动MObileSim
2. roscore
3. rosrun rosaria RosAria
4. rosrun rosaria rob_key

### Gazebo

可以阅读[wiki](http://wiki.ros.org/sig/robots/Pioneer) and [pioneer3at](https://github.com/dawonn/ros-pioneer3at)

## 声呐使用

在启动rosaria节点后声呐也被启动成topic发布消息，可以直接使用。

## 激光使用

激光为LMS1XX系列，激光自带`ip`地址，需要和机器人进行连接。

在机器人端：
​	在`Ubuntu`下“编辑连接”——“以太网”——“添加”创建一个名为`lms1xx`的有线连接，手动设定子网掩码为`255.255.255.0`，手动设定`IP` `192.168.0.10`（`192.168.0.2~192.168.0.254`都行），其他设为`0`就行。这样此`IP`与`LMS1xx`设备`IP`就处于同一网段了 。此时wifi选择链接到实验室，将有线链接，激光与机器人即可通信。

强烈不建议`apt-get`安装lms1xx，选择如下：

```
$gitclonehttps://github.com/ncnynl/LMS1xx.git
#编译
$cd~/catkin_ws
$catkin_make
```

LMSxxx激光雷达的的默认IP地址是`192.168.0.1`

最后执行测试：

```
$roscore
$rosrunlms1xxLMS1xx_node_host:=192.168.0.1
$rosrunrvizrviz
```

注意：`_host`可以在`LMS1xx_node`中直接设置好，并不用每次输入。

在`rviz`中选择添加激光topic后可能出现`status`错误，那是因为你发布的激光数据`frame` 并不存在相应的data，你可以修改`LMS1xx_node`节点中激光数据发布主题的`frame`为`odom`， 然后在`rviz`中选择此`frame` 即可查看激光数据。

## kinect V2使用

## 实验

## 后记



