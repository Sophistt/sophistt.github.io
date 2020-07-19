---
title: Multiple Publishers and Subscribers in ROS
date: 2019-06-17 00:06:12
tags: ROS
categories: Robot
---

Regularly, it is necessary to subscribe different topic messages and publish one or more messages in one ROS node. Therefore,
we need to design a node which owns several publishers and/or subscribers. Sometimes we will even use multi-thread methods in C++.

# Python

The following codes shows how to subscribe a topic message and publish another topic message in one node. This method utilizes reponsion mechanism, it will publish a message after it receive a topic message and finish dealing with it.

```python
#!/usr/bin/python

import rospy
from std_msgs.msg import Float32
from customed_msgs.msg import Gpsmsg

pub = rospy.Publisher("/control_info", Float32, queue_size=10)

def gps_callback(message):
    global pub
    lat, lon = message.lat, message.lon
	
    # do something here
	
    msg = Float32()
    msg.data = 10
    pub.publish(msg)	

def main():
    rospy.init_node('talker', anonymous=True)

    gps_localization_sub = rospy.Subscriber("/gps_info", Gpsmsg, gps_callback)

    rospy.spin()


if __name__ == '__main__':
    try:
        main()
    except rospy.ROSInterruptException:
        pass

```

If we have a `while` circulaiton and would like to do somthing periodically in it, meanwhile we have to subscribe different types of messages. We can use the following codes.

```python
#!/usr/bin/python
# -*- coding: utf-8 -*-

"""
Subscribe two control signals and use matplotlib to draw them dynamically for comparsion.
"""

import rospy
from std_msgs.msg import String, Float32

import matplotlib.pyplot as plt
import numpy as np
import time
from math import *


count = 0
angle_list = []
time_list = []

sCount = 0
sAngle_list = []
sTime_list = []


def draw_angle_callback(angle):
    global angle_list, count, time_list

    if len(angle_list) > 300:
        angle_list.pop(0)
        time_list.pop(0)

    angle_list.append(angle.data)
    time_list.append(count)

    count += 1

def draw_sAngle_callback(sAngle):
    global sCount, sAngle_list, sTime_list

    if len(sAngle_list) > 300:
        sAngle_list.pop(0)
        sTime_list.pop(0)

    sAngle_list.append(sAngle.data)
    sTime_list.append(sCount)

    sCount += 1

def main():
    rospy.init_node("draw_steering_angle", anonymous=True)
    steering_angle_sub = rospy.Subscriber("/steering_angle", Float32, draw_angle_callback)
    steering_smooth_angle_sub = rospy.Subscriber("/smooth_angle", Float32, draw_sAngle_callback)

    plt.ion() # Start up the interacitve mode
    plt.figure(1)

    while not rospy.is_shutdown():
        plt.clf()
        plt.ylim((-100, 100))
        plt.plot(time_list, angle_list, 'r',  linestyle="--")
        plt.plot(sTime_list, sAngle_list, '-b')
        plt.pause(0.01)

    rospy.spin()

if __name__ == "__main__":
    try:
        main()
    except rospy.ROSInterruptException:
        pass

```

# C++

In C++, I think it is a good habit to use a `class` to represent a Ros node. Various publishers and subscribers are differnt class members in it.
In its constuction funciton, all these publishers and subscribers will be initialized. The following codes show the whole procedure of work flow.
(**Similar to the first example above.**)

```c++
#include "ros/ros.h"
#include <boost/thread.hpp>
#include "std_msgs/Float32.h"
#include "customed_msg/Gps.h"

class WorkNode
{
public:
  WorkNode()
  {
	  gpsSub = nh.subscribe("gps", 1 &WokrNode::gpsSubCallback, this);
	  anglePub = nh.advertise<std_msgs::Float32>("/angle", 10);
  };
  ~WorkNode();

  void gpsSubCallback(const customed_msg::Gps::ConstPtr& gpsMsg);

private:
  ros::NodeHandle nh;
  ros::Subscriber gpsSub;
  ros::Publisher anglePub;
  std_msgs::Float32 angle;
};

void WorkNode::gpsSubcallback(const customed_msg::Gps::ConstPtr &gpsMsg)
{
	# Do something here.
    
	angle.data = 10.0;
	anglePub.publish(angle);
};

int main()
{
  ros::init(argc, argv, "work_node");

  WorkNode *workNode = new WorkNode();

  ros::AsyncSpinner spinner(2); // Use 2 threads
  spinner.start();
  ros::waitForShutdown();

  delete workNode;
  return 0;
}

```

# References

- [Writing a Simple Publisher and Subscriber (Python)](http://wiki.ros.org/ROS/Tutorials/WritingPublisherSubscriber%28c%2B%2B%29)
- [Writing a Simple Publisher and Subscriber (C++)](http://wiki.ros.org/ROS/Tutorials/WritingPublisherSubscriber%28c%2B%2B%29)
