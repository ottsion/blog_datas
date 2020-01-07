---
date: 2017-06-16T23:59:59+00:00
draft: false
author: "caicai"
title: " ROS学习之rqt_plot绘图"
categories: ["ROS"]
tags: ["ros"]
---

## ROS学习之rqt_plot绘图##



> 速度加速度、误差等的曲线图

显示命令到terminal中，然后等到动作结束，用鼠标拷贝出来到txt文件，而且，最重要的问题是，由于滚屏，你只能拷贝部分运动信息，很快就舍弃这种做法了。

后来，就想起以前用命令行重定向将信息保存到了文本中，于是就很好的解决了数据保存问题。

`rostopic echo /joint_states > record2.txt  `

#### 1. 背景

因为在为youbot添加FollowJointTrajectory 的client之后，仿真环境中的动作与实际有出入，因此想看看机械臂的实际动作曲线。而在ROS中，内置的rqt_plot是可以提供绘图功能的，而后来发现rqt_plot的功能比较简单，随着数据量的增大，查看起来比较费劲。于是就有了后来的导入matlab中分析的部分。

#### 2.rqt_plot绘图

照例，我们找到官网的相关部分，有个基础理解：[http://wiki.ros.org/rqt_plot](http://wiki.ros.org/rqt_plot)

```
rqt_plot /turtle1/pose/x:y:z  

rqt_plot /turtle1/pose/x /turtle1/pose/y /turtle1/pose/z  
```

特别

注意

上面的第一句，它就是下面的简写，同一个topic下的直接冒号就好，不用输入那么多的文字。

在这里我为了避免每次要去动机械臂，将joint_states这个topic的信息已经保存为了bag文件，**录的动作是我上一篇文章，actionlib中的动作的一部分，大概是从home到伸直的一部分**。使用指令（参看《a gentle introduction to ROS》P135页）

```rosbag record -O youbot_action2.bag /joint_states  ```

-O(是字母O, 并非数字0，它表示输出文件（output），后面跟的是你的输出文件的名字，我这里是youbot_action2.bag)，下面是我的bag的一些基本信息：

一共21.1s，包含了861条信息

![img](http://img.blog.csdn.net/20150506131445167)

我们查看一下数据内容

![img](http://img.blog.csdn.net/20160118222946817)

然后开启一个terminal，回放数据rosbag play youbot_action2.bag --clock，--clock表示使用simulate time，具体可以参考[http://wiki.ros.org/Clock](http://wiki.ros.org/Clock) 。再打开另外一个，开启rqt_plot，用命令
```
rqt_plot /joint_states/velocity[0]:velocity[1]:velocity[2]:velocity[3]:velocity[4]
```
因为，youbot机械臂是五个自由度，这里我们用标号0-4来引用每一个（用QwtPlot画出来的）

![img](http://img.blog.csdn.net/20150506130947581)

用MatPlot画出来的，可以看出他们刚好是左右颠倒的，我们的这个bag文件，有大概20s。右键按住鼠标上下或左右滑动进行缩放。

![img](http://img.blog.csdn.net/20150506131200088)

同理，我们也可以找出各个关节的一些信息：

`rqt_plot /joint_states/position[0]:position[1]:position[2]:position[3]:position[4]`  

得到的图像

![img](http://img.blog.csdn.net/20150506132107345)

![img](http://img.blog.csdn.net/20150506131831617)

图像很好看，但是实际分析起来是很头疼的，它只能是看个大概过程，了解基本曲线的动作和大概的规律，比如速度曲线的不平滑，关节位置曲线的大概变动范围（起始位置并不是0，依次是0.11，0.11，-0.11，0.11，0.12 rad）

#### 3.Matlab绘图

其实，这个地方我也走了点弯路的，当时我用命令
`rostopic echo /joint_states`

显示命令到terminal中，然后等到动作结束，用鼠标拷贝出来到txt文件，而且，最重要的问题是，由于滚屏，你只能拷贝部分运动信息，很快就舍弃这种做法了。

后来，就想起以前用命令行重定向将信息保存到了文本中，于是就很好的解决了数据保存问题。

`rostopic echo /joint_states > record2.txt`

将txt文件拷贝出来到windows上，导入excel文件，然后借助Matlab分析（这里主要是用到了seq，position和velocity）

有同学可能就会问了，明明文件中有时间信息，人家还精确到了ns，干嘛不用呢，这里有个小插曲，当时用matlab读取的时候，由于[Linux](http://lib.csdn.net/base/linux)系统中的时间戳信息是从1970年的秒数开始计算的，数据长度很大，导致Matlab处理的时候直接用科学计数法来统计，后面的几位数都直接给过滤掉了，因而导致画出来的图明显不符合逻辑，一个时间点对应多个关节值。

因此，由于每个数据点都有自己的标号，我就简化为关节信息和点序号的对应信息了。
![img](http://img.blog.csdn.net/20150506142439714)

![img](http://img.blog.csdn.net/20150506142545182)

![img](http://img.blog.csdn.net/20150506142617574)![img](http://img.blog.csdn.net/20150506142649846)

#### 4. 总结

这里只是把自己的一些过程展示出来，抛砖引玉，具体的数据分析，后面还有待进一步深入。我还没有找其他的机械臂对比一下速度曲线，youbot的跟我预想的不太一样，也可能是由于参数信息的设置导致的，后面我会继续深入下去。

另外，借助于ROS本身的rqt_plot，我们看到数据图像，与matlab基本相同，对于，只是想看看大概规律来说，功能也够用了。
