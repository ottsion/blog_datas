---
date: 2017-06-20T23:59:59+00:00
draft: false
author: "caicai"
title: " ROS中Dynamxel舵机控制"
categories: ["ROS"]
tags: ["ros, Dynamxel"]
---

​				

# 1 拷贝程序文件

> cd ~/joey_ws/src
> git clone [https://github.com/arebgun/dynamixel_motor.git](https://github.com/arebgun/dynamixel_motor.git)

# 2 编译文件

> cd ~/joey_ws
> catkin_make

# 3 设置启动驱动launch文件

> vim ~/joey_ws/src/dynamixel_motor/dynamixel_tutorials/launch/controller_manager.launch

```
    <!-- -*- mode: XML -*- -->
    <launch>
        <node name="dynamixel_manager" pkg="dynamixel_controllers" type="controller_manager.py" required="true" output="screen">
            <rosparam>
                namespace: dxl_manager
                serial_ports:
                    pan_tilt_port:
                        port_name: "/dev/ttyUSB0"
                        baud_rate: 1000000
                        min_motor_id: 1
                        max_motor_id: 25
                        update_rate: 20
            </rosparam>
        </node>
    </launch>
```

# 4 运行驱动检测舵机

连接USB2Dynamixel 运行Dynamixel控制器
USB2Dynamixel连接USB接口，舵机连接USB2DYnamixel接口，舵机外部供电

> roslaunch dynamixel_tutorials controller_manager.launch

ID 搜索默认从1~25 若查找不到，可以更改controller_manager.launch将ID搜索扩大

> rostopic list

出现--> /motor_states/pan_tilt_port 则连接成功

# 5 设置舵机驱动文件

> vim ~/joey_ws/src/dynamixel_motor/dynamixel_tutorials/config/dynamixel_joint_controllers.yaml

```
    pan_controller:
        controller:
            package: dynamixel_controllers
            module: joint_position_controller
            type: JointPositionController
        joint_name: pan_joint
        joint_speed: 2.0
        motor:
            id: 5
            init: 512
            min: 0
            max: 1023

    tilt_controller:
        controller:
            package: dynamixel_controllers
            module: joint_position_controller
            type: JointPositionController
        joint_name: tilt_joint
        joint_speed: 2.0
        motor:
            id: 6
            init: 512
            min: 0
            max: 1023
```

> vim ~/joey_ws/src/dynamixel_motor/dynamixel_tutorials/launch//controller_spawner.launch

```
    <!-- -*- mode: XML -*- -->
    <launch>
        <!-- Load controller configuration to parameter server -->
        <rosparam file="$(find dynamixel_tutorials)/config/dynamixel_joint_controllers.yaml" command="load"/>

        <!-- start specified joint controllers -->
        <node name="dynamixel_controller_spawner" pkg="dynamixel_controllers" type="controller_spawner.py"
              args="--manager=dxl_manager
                    --port=pan_tilt_port
                    --type=simple
                    pan_controller
                    tilt_controller"
              output="screen"/>
    </launch>
```

# 6 运行舵机驱动话题

> roslaunch dynamixel_tutorials controller_spawner.launch
> rostopic list

出现：/tilt_controller/command /pan_controller/command 则运行成功

# 7 控制舵机

> rostopic pub /pan_controller/command std_msgs/Float64 -- 1.5
> rostopic pub /tilt_controller/command std_msgs/Float64 -- 1.5
