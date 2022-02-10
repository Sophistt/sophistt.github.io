---
title: ROS Launch Exploration
date: 2019-07-05 10:35:05
tags: ROS
categories: Robot
---

在 Ros 中，要启动多个节点的时候，如果要一个一个节点的去启动，将十分麻烦而且浪费时间。因此 Ros 提供了 **roslaunch** 方法来同时启动多个节点。**roslaunch** 在启动任何一个节点前，都会确定 **roscore** 节点是否已经在运行，如果没有则自动启动它。

要使用 **roslaunch** 方法，首先需要编写 `.launch` 文件，该文件以 `xml` 的格式编写。 

# Basic knowledge

**roslaunch** 启动多个节点的时候，**无法控制节点的启动顺序**。因此，如果需要顺序启动节点的话，可以使用`bash script`来控制节点的启动。

`launch` 文件的存放位置并不做要求，也就是说一个`launch`文件可以启动多个 **workspaces** 内的多个 **packages**。但是在使用 **roslaunch** 命令调用 `launch` 文件之前，必须通过 `source ws/devel/setup.bash` 将该 **workspace** 加入到环境变量中，
否则会出现 **Package not found** 的错误。

因此，推荐的运行 `launch` 文件的方式为：

```bash
# In launch file directory
source /path/to/ws/devel/setup.bash
roslaunch ./example.launch
```

# Example

一个基础的 `.launch` 文件如下：
```xml
<launch>
    <arg name="csv_path" default="/home/wcy/code/gac_path_track/path_data/csv/huagong-donghu-osm.csv"/>
  
    <group ns="pathTrack">
        <node pkg="path_track" name="path_track_node" type="path_track_node" output="screen" launch-prefix="xterm -fa 'Monospace' -fs 10 -e" />
        <node pkg="pub_path" name="pub_path_node" type="talker.py" args="$(arg csv_path)" output="screen" launch-prefix="xterm -fa 'Monospace' -fs 10 -e"/>
    </group>

</launch>
```

# Example explained

`<launch> ... </launch>`: 根元素(root element), 作为放置其他元素的容器，其他元素必须放在该标志之间。 <br>
`<arg name="" default=""/>`: 设置全局变量，通过`name`和`default`分别设置变量名和值，在节点启动需要参数时可以使用。
`<group ns=""> ... </group>`: 组，将制定的节点组织起来，令其使用相同的命名空间从而有效管理多个节点。 <br>
`<node pkg="" name="" type="">`: 定义想要启动的 ROS 节点。包含三个必须的属性，`pkg`，`type`，`name`。同时还有`output`，`launch-prefix`等可选属性。
- `pkg`: 节点所在的程序包的名字。
- `type`: 节点可执行文件的名字。
- `name`: 节点名，该属性覆盖原有文件中 `ros::init` 的 `name`。
- `args`: 如果该节点启动的时候需要输入参数，可以通过`args`传入参数。
- `output`: 指定该节点输出的位置，`screen`设置为当前终端，也可以设置成将内容输出到指定文件内。
- `launch-prefix`: 该属性指定开启新的窗口显示输出内容，`xterm -e` 或 `gnome-terminal` 分别是使用不同的终端显示内容。

# References

- [roslaunch/XML](http://wiki.ros.org/roslaunch/XML)
- [roslaunchCommandline/Tools](http://wiki.ros.org/roslaunch/Commandline%20Tools)
- [ROS学习之roslaunch](http://ttshun.com/2018/05/24/ROS%E5%AD%A6%E4%B9%A0%E4%B9%8Broslaunch/)