---
title: Use Customed Python Environment in ROS
date: 2019-06-14 00:00:43
tags: ROS
categories: Robot
---

# Install Denpendent Packages

In ROS, if we use our special python environment under `conda` or `virtualenv` instead of the basic python environment, 
there will be something wrong when we use ROS package. This is because ROS packages cannot be found in our special environment.
In order to resolve this problem, we need to install ROS packages in our python enviornment. Open a terminal and enter the following codes to install ROS dependencies.

```bash
$ conda activate yourEnv
$ pip install catkin-tools
$ pip install rospkg
```

# Specify the Python Interpreter

In order to use our special environment, we have to assign the python interpreter in our python file. Add the following code on the first line of your python file.

```python
#! /path/to/python3
```