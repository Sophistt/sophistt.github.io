---
title: Self-driving Hardware and Software Architectures
date: 2019-09-24 14:02:34
mathjax: true
tags:
categories:
  - Study
  - Autonomous Driving
---

本篇为多伦多大学的自动驾驶车辆课程中的第二周课程笔记，涉及到自动驾驶车辆的软硬件设计。

# Sensors and Computer Hardware 

## Sensors

传感器(Sensors)作为检测外界和车辆自身情况的硬件，在无人驾驶车辆中是必不可少的。传感器可以根据其感知数据范围的不同分为两类：

- **Exteroceptive**：extero = surroundings
- **proprioceptive**：proprio = internal

### 摄像机

摄像机(Camera)作为感知外界环境的核心，主要有以下3个评价指标(Comparison metrics):

- **分辨率(Resolution)**：分辨率影响摄像机的清晰度，分辨率越高，则可以观察到的物体细节越多
- **视场角(Field of View)**：视场角顾名思义为摄像头可以观察到的范围
- **动态范围(Dynamic Range)**：动态范围指的是摄像头从黑暗到明亮环境时的成像调节能力

有的摄像机通过两个镜头加上融合算法处理，可以得到深度信息，被称作双目摄像机，得到出来的图像除了三个颜色通道外(RGB)，
还有一个通道存储深度信息(Depth)。

### 激光雷达

激光雷达(Lidar)在无人驾驶车辆中也得到很好的应用，通过测量激光反射的强度和时间形成的激光点云(Point Cloud)数据可以很好的反映周围环境的几何信息。
主要的评价指标为：

- **线数(Number of beams)**：线数越多的Lidar，检测物体的精度也越高，常见的线数有8, 16 ,32 ,64
- **每秒点数(Points per second)**：每秒点数决定了Lidar的检测数率
- **旋转速率(Rotation Rate)**：旋转速率指的是Lidar旋转一圈所需要的时间
- **视场角(Field of View)**：视场角为Lidar的上下可观察到的范围

<img src="https://i.imgur.com/yIosdBv.png" alt="激光雷达点云图"/>

### 毫米波雷达

毫米波雷达(Radar)在无人驾驶中主要应用与目标检测和相对速度测量，也分为WFOV(短距)和NFOV(长距)两类：

- **探测范围**：最大的有效检测距离
- **视场角**
- **检测精度(Accuracy)**：检测精度指的是检测位置(Position)以及速度(Speed)的精度

### 超声波雷达

超声波雷达(Ultrasonics)可以提供短距的，全天候下的距离检测，而且不会受到外界光照等条件的影响，是低成本自动泊车解决方案的必备选择，
主要缺点为探测距离短。

- **探测范围**
- **视场角**
- **成本(Cost)**

### 定位模块

在无人驾驶车辆中，定位模块由GNSS模块(Global Navigation Satellite Systems)和IMU模块(Inertial Measurement Units)组成, 其中GNSS模块提供车辆的经纬度，航向角信息；IMU模块提供车辆的3轴角速度，角加速度，加速度等信息。

### 轮速计

轮速计可以通过测量车辆转速与摆角得到较为精确的车辆速度与车辆朝向，也是无人驾驶车辆可以使用的传感器之一。

## Computing Hardware

当前成熟的，符合车规级的自动驾驶硬件平台并不多，其中最具有代表性的就是 **NVIDIA PX/AGX** 和 **Intel & Mobile EyeQ**。 

硬件平台需求： 因为需要实时处理大量来自Lidar，Camera的数据，甚至需要进行深度学习，因此**GPU**和**FPGAs**是其中的核心部件之一。

# Hardware Configuration Design

在硬件选型以及设计中，核心环节之一是传感器的布置方案。 下面将探讨如何在无人驾驶车辆上布置上文提到的6种传感器，从而保证行车安全。

## 需求分析

可以将无人驾驶分解为两种基础场景，高速道路行驶以及城市道路行驶：

### 高速道路行驶

高速道路行驶场景中，无人驾驶车辆主要处理以下三个任务：

- 紧急停车(Emergency Stop)： 假设车速为 120 *kmph*, 紧急停车制动距离约为 120 *meters*
- 速度保持(Matain Speed)： 高速行驶中，为了维持车速和保持足够的制动距离，需要和前车保持的距离约为 100 *meters*
- 变道(Lane Change)： 为了顺利变道，传感器应当探测临近车道及其过去的一个车道，防止变道过程中发生碰撞

<img src="https://i.imgur.com/zIT5QpK.png" alt=">传感器横向探测图"/>

## 城市道路行驶

城市道路行驶场景中，无人驾驶车辆**不仅需要**关注高速道路行驶的任务，**还需要**关注以下三个额外的任务：

- 绕障(Overtaking)：在绕障过程中，需要的传感器与高速公路场景下的变道相似
- 转弯，通过路口(Turning, Crossing at Intersections)：通过路口时，自动驾驶车辆不仅需要关注对向车i狼，还需要关注横向来车。

<img src="https://i.imgur.com/IrMhtp4.png" alt="十字路口传感器探测图"/>

- 通过环岛(Passing Roundabouts)：因为环岛的特殊性，车辆的速度会比平时更低，同时需要一个视场角更宽的传感器以提高探测效果。

<img src="https://i.imgur.com/yqqkdIU.png" alt="环岛传感器探测范围图"/>

### 传感器布置图

结合高速道路驾驶场景与城市道路驾驶场景，自动驾驶车辆传感器布置图的模式如下：

<img src="https://i.imgur.com/sF24xcJ.png" alt="传感器总布置图"/>

# Software Architecture

自动驾驶汽车的软件基础架构可以分为5大模块，分别为：**环境感知**，**环境匹配**，**动作规划**，**动作控制**，**系统监察**。

<img src="https://i.imgur.com/vhDwQc6.png" title="source: imgur.com" alt="自动驾驶软件架构"/>

## 环境感知

环境感知模块需要接收几乎所有的传感器输入，从而完成**自定位**，**动态目标检测**和**静态目标检测**三个功能。

- 自定位：根据 GPS/IMU/Wheel Odometry 等传感器的输入，得到车辆的位置信息
- 动态目标检测：根据 Lidar/Cameras/Radar 的传感器的输入，不仅需要完成**目标识别**，还需要完成**目标跟踪**和**目标预测**。
- 静态目标检测：根据 HD Road Map 和 Lidar 的数据，识别出地面标志，Road Signs 等指示标志和路沿等障碍。

## 环境匹配

环境匹配模块和环境感知模块密切相关，其任务是利用和传感器的数据和环境感知模块的结果，根据已经录制好的高精地图，更精确地匹配出车辆所在位置，得到详细的周边环境信息。
其模块包含3个类型的地图。

- 栅格地图(Occupancy Grid Map)：栅格地图将地图划分为小格，并且将静态障碍物和可通行区域划分为不同的属性，利用该地图可以查询到可通行区域。
- 定位地图(Localizaiton Map)：定位地图有预先采集到的点云构成，通过将实时扫描到的点云与定位地图进行匹配，可以得到精度极高的自定位信息。
- 道路地图(Detailed Road Map)：道路地图一般以拓扑图的形式存在，将车道等信息连接起来，通过查询道路地图可以得到从A到B点的最优全局导航路径。

## 动作规划

动作规划模块是环境感知模块的下游，输入环境感知模块的结果，根据当前车辆所处的环境，输出当前时刻的最优轨迹到动作控制模块中。动作规划模块一般有三个子模块构成，
分别是**任务规划**，**行为规划**，**局部轨迹规划**三个模块。

- 任务规划(Mission Planner)：任务规划模块利用当前车辆位置信息和**道路地图**，根据所给出的终点，使用算法得到合理的全局路径
- 行为规划(Behaviour Planner)：行为规划模块，根据全局导航路径和检测到的动态，静态障碍物，做出合适的行为决策，指导无人驾驶汽车行驶
- 局部轨迹规划(Local Planner)：局部轨迹规划模块根据行为规划模块的输出结果，根据车辆特性和驾驶舒适性等要求，输出合理的行车轨迹

<img src="https://i.imgur.com/dpKbzxb.png" title="source: imgur.com" alt="动作决策模块架构图"/>

## 系统监察

系统监察模块主要是利用运行在自动驾驶系统之外的另外一套系统来监听自动驾驶系统的各个模块运行，通讯是否正常，同时监管每个传感器是否正常工作，在传感器或者自动驾驶系统失效的情况下，监察系统需要**发出告警**且退出自动驾驶模式。

# Environment Representation

环境表征主要分为将周围环境分为三个地图，分别为**栅格地图**,**定位地图**和**道路地图**，在前面的*环境匹配*模块中已经进行过介绍，此处不再赘述，可以在 **Refernces** 的 *Environment Representation* 进行查看。

# References

- [Sensors and Computer Hardware](https://d3c33hcgiwev3.cloudfront.net/qOzjLiP0Eem9HA6xGGaRfg_a92f911023f411e9b54e6d5a03316a13_C1M2L1---Sensors-and-Computing-Hardware.pdf?Expires=1569456000&Signature=Rpji2USvyDoqtj1aSkHXWnYHq1D2NIyt6wOf6-Z5YCVSma32WldGhXtmqrqLFE96s8nHAMZ0iuV5A-GsA6mEghxltC~Q~TkGlqTsw1s-7oCBEUKCkDC4CwOvQzl~cCjzu3fv3Jev7aE5Mf-ulltznh6g9De05D~~QP8q~aatO6g_&Key-Pair-Id=APKAJLTNE6QMUY6HBC5A)
- [Hardware Configuration Design](https://d3c33hcgiwev3.cloudfront.net/rqAg6SP1EemdvRIYWfQePg_aee0846023f511e9ac6401043cf68a79_C1M2L2---Hardware-Configuration-Design.pdf?Expires=1569456000&Signature=DI1Hgzlq4ydcBcdQjf2~zaL2BOx4-X8tP269lLA7TbQjmnXRqutAT2J-3-D394e5bT4fvsOdHBp9etDAP7IVQoJ6FZu72FxfbBz3TheLhXUM6nUqY6o2KkftxNAsGlM5vvUivWe-twYMyjQ8sjvn4t~ccL2zvgDw9YyPqqj6Nx8_&Key-Pair-Id=APKAJLTNE6QMUY6HBC5A)
- [Software Architecture](https://d3c33hcgiwev3.cloudfront.net/SE0ZMCP2EemdvRIYWfQePg_48711bd023f611e9b54e6d5a03316a13_C1M2L3---Autonomous-Vehicle-Software.pdf?Expires=1569628800&Signature=aQ9LzEWYB4gjQJQ8FoX5ZhlBDx8ZDI4DDn5eebnuW7nUPE643iu12DWMMcQ8YTAl6ic1M~3cezgL-zcWqGJLK~~LE7PrZOz~c0L018T33HF63d9xWPASnj8Mb~I3nPacfhwNgJfYvwXhlqsX9Y5A6kJh5zwMVgJ5WnD4Wwjx5rI_&Key-Pair-Id=APKAJLTNE6QMUY6HBC5A)
- [Environment Representation](https://d3c33hcgiwev3.cloudfront.net/SE2OYSP2EemdvRIYWfQePg_4881bda023f611e9bdd6579bce97d1d3_C1M2L4---Environment-Representation.pdf?Expires=1569628800&Signature=HqCrkIUjxVVDJu43Ua3Sfhj1yDf6OKxiMBroTbErsdzb-j3nsYJ7qCGnL~5EKxGNEHYnWzA05hIkmKGpeJzJh63uW6gBgErsZdFdG~G1SjNA8TBvM6JprDW6ZZv3DXlBMpmVSQSgY6-22rayhoQN5LiUTvxwF-cCJ-01HTDfsMI_&Key-Pair-Id=APKAJLTNE6QMUY6HBC5A)