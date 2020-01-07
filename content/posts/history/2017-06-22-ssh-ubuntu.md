---
date: 2017-06-22T23:59:59+00:00
draft: false
author: "caicai"
title: " ROS多电脑之间的通信"
categories: ["ROS"]
tags: ["ros, ssh"]
---



最终要实现在 desktop_hostname 电脑上运行roscore，并且运行 Turtlesim 程序，然后在 laptop_hostname 电脑上运行键盘控制程序，控制 Turtlesim 小乌龟移动。

在pioneer中将机器人端视为desktop_hostname，自己的笔记本为B。

### 一. 查看电脑ip###

1. 利用`ifconfig`查看ip地址。
2. 利用`hostname`指令查看hostname。

### 二. 修改hosts文件

使用如下指令，分别打开 hosts 文件：

```
$ sudo gedit /etc/hosts
```

在**电脑desktop_hostname端**的 hosts文件 中添加如下加粗指令:

```
127.0.0.1　　　    localhost
127.0.1.1         [ desktop_hostname ]
[IP_A]            [ desktop_hostname ]
[IP_B]            [ laptop_hostname ]
```

同理，在**电脑laptop_hostname端**的 hosts 文件中，加入如下加粗指令：

```
127.0.0.1　　　    localhost
127.0.1.1         [ laptop_hostname ]

[IP_B]            [ laptop_hostname ]
[IP_A]            [ desktop_hostname ]
```

### 三. 电脑之间通信

1. 在两台电脑上装上chrony包，用于实现同步：

```
sudo apt-get install chrony  
```

2. 安装ssh

```
sudo apt-get install openssh-server  
```

3. 确认启用

```
ps -e|grep ssh  
```

如果只有ssh-agent，那ssh-server还没有启动，需要/etc/init.d/ssh start，如果看到sshd那说明ssh-server已经启动了。

互相访问查看通信正常：

```
ssh desktop_hostname  
ping laptop_hostname  
```

4. 修改`.bashrc`文件

```
gedit ~/.bashrc  

# desktop_hostname名下：
export ROS_HOSTNAME=desktop_hostname  
export ROS_MASTER_URI=http://laptop_hostname:11311  

# laptop_hostname名下：
export ROS_HOSTNAME=laptop_hostname  
export ROS_MASTER_URI=http://laptop_hostname:11311  
```

**注意**：我们这里的添加方式里面ROS_MASTER_URI都是**指向**的laptop_hostname的主机名，说明是在laptop_hostname上运行roscore

### 四. 测试ROS通信

1. laptop_hostname端打开`roscore`

2. desktop_hostname端输入：

```
rosrun turtlesim turtlesim_node
```

3. 笔记本端输入：

```
rosrun turtlesim draw_square 
```


这时候你就可以看到台式机上的小渔龟正在走正方形了。
