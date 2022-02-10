---
title: data-visualization-through-matplotlib-1
date: 2019-06-12 15:51:06
tags: visualization
categories:
  - Programming
  - Python
---

# Line Chart

In order to show data dyanmically, it's necessary to use interactive mode of **matplotlib**. The following codes show a sine line dynamically.

```python
import matplotlib.pyplot as plt
import numpy as np
import time
from math import *

count = 0
time_list = []
data_list = []

def main():
    plt.ion()  # Use interactive mode
    plt.figure(1)

    for:i range(2000):
        plt.clf()  # clear the canvas
        time_list.append(0.1 * i)
        data_list.append(sin(0.1 * i))
        plt.plot(time_list, data_list, '-r')
        plt.pause(0.01)

if __name__ == '__main__':
    main()

```

# Scatter Graph

```python
import matplotlib.pyplot as plt
import numpy as np
import time
from math import *

def main():
    plt.ion()  # Use interactive mode
    plt.figure(1)

    for i in range(2000):
        plt.scatter(i * 0.1, sin(i * 0.1))
        plt.pause(0.01)

if __name__ == '__main__':
    main()

```

# Receive and Show Data from ROS Topic

In my ROS project, I need to receive messages from topic and analyze them, so I write down the following codes to remind myself how to finish the whole procedure.

```python
#!/usr/bin/env python
# -*- coding: utf-8 -*-

import rospy
from std_msgs.msg import String, Float32

import matplotlib.pyplot as plt
import numpy as np
import time
from math import *

count = 0
angle_list = []
time_list = []

def draw_angle_callback(angle):
    global angle_list, count, time_list
    
    # Pop history data
    if len(angle_list) > 300:
        angle_list.pop(0)
        time_list.pop(0)

    angle_list.append(angle.data)
    time_list.append(count)

    count += 1

def main():
    # Initialize a ros subscribe node
    rospy.init_node("draw_steering_angle", anonymous=True)
    steering_angle_sub = rospy.Subscriber("angle", Float32, draw_angle_callback)

    plt.ion()    # Interacitve mode
    plt.figure(1)

    while not rospy.is_shutdown():
        plt.clf()
        plt.ylim((-100, 100))
        plt.plot(time_list, angle_list, 'r',  linestyle="--")
        plt.pause(0.01)

    rospy.spin()

if __name__ == "__main__":
    try:
        main()
    except rospy.ROSInterruptException:
        pass
```

# References:
- [python中plot实现即时数据动态显示方法](https://blog.csdn.net/u013468614/article/details/58689735)
- [Matplotlib 画动态图: animation模块的使用](https://blog.csdn.net/u013180339/article/details/77002254)
