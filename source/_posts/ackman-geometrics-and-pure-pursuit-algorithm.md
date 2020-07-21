---
title: Ackermann Geometrics and Pure Pursuit Algorithm
date: 2019-06-14 15:15:48
mathjax: true
tags: 
categories: 
  - Study
  - Autonomous Driving
---

# Ackermann Geometrics

如下图，车辆以固定的方向盘转角转弯时，实质为绕圆心在后轴延长线上，半径为 $r$ 的圆弧做圆周运动。

<img src="https://i.imgur.com/V86UOKj.png" title="Ackman image"/>

根据图内关系，可以推算出车辆前轴偏转角与圆心半径 $r$ 的关系, $L$为车辆轴距。

$$
\delta = arctan(\frac{L}{r})
$$

# Pure Pursuit Algorithm

Pure Pursuit Algorithm 是一种车辆跟踪的简单算法，具体方法为根据车辆当前点与一系列的waypoints，与当前车辆距离合适的一个目标点，计算车辆从当前位置驾驶到目标点位置的期望轮胎转角。


<img src="https://i.imgur.com/V86UOKj.png" title="Ackman image"/>


由上图，以车辆后轴为圆心建立车辆坐标系，经过几何运算可以得到车辆圆周运动的圆弧曲率 $\kappa$ 与目标点位置的关系。

$$
d = r-x \\
(r-x)^2 + y^2 = r^2 \\
r^2 - 2rx + x^2 + y^2 = r^2 \\
2rx = l^2 \\
r = \frac{l^2}{2x} \\
\kappa = \frac{2x}{l^2}
$$

Pure Pursuit 是一种很直观的算法，因此该算法的关键从 Tracking Path Points 中选取一个合理的目标点，目标点与车辆的距离用 $l$ (look ahead distance) 来表示。如果 $l$ 取值太小，则跟踪曲线会在实际路径附近来回振荡，如果 $l$ 取值太大，则会跟踪得太慢而影响跟踪效果。


<img src="https://i.imgur.com/th59YZi.png" title="look ahead distance"/>

# References

- [Ackermann steering geometry](https://en.wikipedia.org/wiki/Ackermann_steering_geometry)
- [Implementation of the Pure Pursuit Path Tracking Algorithm](https://www.ri.cmu.edu/pub_files/pub3/coulter_r_craig_1992_1/coulter_r_craig_1992_1.pdf)