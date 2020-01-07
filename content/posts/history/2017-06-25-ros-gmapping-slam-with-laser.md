---
date: 2017-06-25T23:59:59+00:00
draft: false
author: "caicai"
title: "先锋机器人下gmapping测试运行"
categories: ["ROS"]
tags: ["ROS, pioneer"]
---



## 配置前期

这里是我的机器人和电脑环境安装步骤，以下激光slam测试前提是进行了[先锋机器人使用手册](https://ottsion.github.io/2017/06/24/pioneer-laser.html) 的所有包安装及运行测试

## 下载gmapping包

```
 sudo apt-get install ros-indigo-slam-gmapping
```

## 运行步骤

1. 笔记本端启动

   ````
   roscore
   ````

2. 远程ssh连接到机器人，启动机器人：  

   ```
   ssh robot@pioneer
   rosrun rosaria ROSAria _port:=/dev/ttyS0
   ```

   如果遇到权限问题参考上一篇：`sudo chmod a+rw /dev/ttyS0`

3. 启动激光数据，发送topic为`/scan` :

   ```
   rosrun lms1xx LMS1xx_node _host:=192.168.0.1
   ```

   这里注意之前说过激光和机器人也是通过网线连接，各自有ip地址，所以我是在机器人端启动的激光指令。（先决条件是激光和机器人连接已经设置好，相关两者网络连接看[先锋机器人使用手册](https://ottsion.github.io/2017/06/24/pioneer-laser.html)） 

4. 笔记本端启动gmapping程序：

   ```
   rosrun gmapping slam_gmapping scan:=scan  
   ```

   前面说过我的激光数据`topic`是`/scan` 。

5. 这里通过rviz查看效果：

   ```
   rosrun rviz rviz  
   ```

   选择添加`/map` 查看，`topic`选择`/map` ，选好`fixed_frame` 。

   如果出现状态错误，可能是因为你的激光与世界坐标间没有`tf`转换，可以添加启动：

   ```
   <launch>
       <node pkg="tf" type="static_transform_publisher" name="map_nav_broadcaster" 
             args="0 0 0 0 0 0 /base_link /laser 100" />
   </launch>
   ```

6. 这时候我们可以启动让小车运动

   ```
   rosrun rosaria rob_key
   ```

   `rob_key`为自己写的，具体写法在[先锋机器人使用手册](https://ottsion.github.io/2017/06/24/pioneer-laser.html)上有说明，就是不同按键发布不同指令。使用上下左右方向键控制小车走动，实现全部建图。
