---
title: Compute Distance and Angle between Two GPS Points
date: 2019-06-10 15:27:47
katex: true
tags: 
categories: 
  - Study
  - Autonomous Driving
---

# 背景知识

## 经纬线的产生与定义

地球是一个椭球体，其赤道半径要比极半径更大。赤道半径 $a = 6378.1730 km$ , 极半径 $b = 6356.7523 km$ ，平均半径 $R = \frac{2a+b}{3} = 6371.0088km$ 。
经纬线则是人们为了清晰表达地球上任意一点而定义出来的辅助线。
纬线定义为地球表面某点随地球自转而形成的轨迹，因此纬线与赤道平行，其周长等于赤道的周长乘以纬线的维度的余弦。
经线定义为地球表面连接南北两极点的半圆弧，任意两经线长度相等，$L = 20003.93km$。

## 经纬度的定义

- 经度：已知经线是连接两极点的圆弧，那么地球上任意一点的经度坐标等于：该点所在经线与地球圆心组成的平面与本初子五线平面的夹角的度数。
- 纬度：已知纬线是与赤道平行的圆弧，因此地球上任意一点的维度坐标等于：该点与球心连接的线段和投影在赤道平面组成的线段的夹角的度数。

| 维度度量 | 相差距离 | 
| :----: | :-----: | 
| 1°     | 111.13km | 
| 1'     | 1.85km   |    
| 1''    | 30.87km  |
| 0.1°   | 11.11km  |
| 0.01°  | 1111.33m |

因此，如果想要距离误差达到米级的话，GPS的精度必须达到 $0.00001°$ 的范围。

# 计算两点距离

目前使用较为广泛的根据GPS计算两点距离的模型为简化模型和球面模型，这两种模型都是对椭球体进行了一定程度的简化。

## 简化模型
简化模型适用于两点距离较近的情形，同时越靠近极地效果越差。简化模型将球面简化为平面，认为经纬线互相垂直。如下图所示，求A，B两点的距离可以被分解为先求A，B两点的横向距离与纵向距离，再根据三角形定律求出A，B的距离。

<div align="center">
<img src="https://i.imgur.com/Oy9qej6.png" title="simplified image"/>
</div>

### 纵向距离
A，B 两点的纵向距离即它们在经线上的差值，通过弧长公式 $L = \pi \alpha$ 分别求出 A，B 两点到赤道线的弧长然后相减即可得到它们的纵向距离。

$$
\begin{align*}
 long_{AB} = \bar{R} * rad({\alpha}^A_{lat} - {\alpha}^B_{lat})
\end{align*}
$$

### 横向距离
A, B 两点的横向距离的计算要纵向的复杂，这是因为纬线的长度随着纬度的增加而减少，所以计算出A，B的横向距离之后还需要乘以角度的余弦值。

$$
\begin{align*}
 lat_{AB} = \bar{R} * cos({\alpha^{\bar{AB}}_{lat}}) * rad({\alpha}^A_{long} - {\alpha}^B_{long})
\end{align*}
$$

### 实现代码(C++)

```c++
#include <cmath>

#define R 6378.137

double getDistance(double lat1, double lng1, double lat2, double lng2)
{
	double lngDistance = R * rad(lat2 - lat1);
	double avgLat = rad(lat1 + lat2) / 2;
	double latDistance = R * cos(avgLat) * rad(lng2 -lng1);

	return sqrt(lngDistance * lngDistance + latDistance * latDistance);
}
```

## 球面模型

原理推导过程比较麻烦，看参考1。


### 实现代码(C++)

```c++
#include <cmath>

#define PI 3.14926535
#define R 6378.137

double rad(double x)
{
	return x * PI / 180.0;
}

double getDistance(double lat1, double lng1, double lat2, double lng2)
{
	if (lat1 == lat2 && lng1 == lng2) return 0;
	
	double havLat = sin(rad(lat1 - lat2) / 2);
	double havLng = sin(rad(lng1 - lng2) / 2);
	double a = havLat * havLat + cos(rad(lat1)) * cos(rad(lat2)) * havLng * havLng;
	
	/**
	* Simplified
	* distance = 2 * R * asin(sqrt(a))
	**/
	distance = 2 * R * atan2(sqrt(a), sqrt(1 - a));

	return distance;
}

```

# 计算两点方位角

A, B两点方位角的含义为：从A，B任一点的指北经线起，按照顺时针的方向旋转到另外一点，期间所转过的角度。可以简单理解为A，B两点连线与指北经线的夹角。
{% note info %}
经纬度分正负，东经为正，西经为负；北纬为正，南纬为负。
{% endnote %}

## 实现代码(C++)

```c++
#include <cmath>

#define PI 3.14926535

double rad(double x)
{
	return x * PI / 180.0;
}

double getAngle(double lat1, double lng1, double lat2, double lng2)
{
	if(lat1 == lat2 && lng1 == lng2)
		return 0;

	double a = rad(lat1 - lat2);
	double b = rad(lng1 - lng2) * cos(rad(lat1));
	double angle;
	
	if (a == 0)
	{
		if(b > 0)  angle = PI / 2;
		else  angle = -PI / 2;
	}	
	else  angle = atan(b / a);
	if (a < 0)  angle += PI

	return angle * 180.0 / PI;
}
```


# 将经纬点转到汽车坐标系下

假设存在A，B两点，A点为车辆自身定位点，B点为车辆外的另外一点，已知A，B两点的经纬度，且知道车辆当前的航向角（航向角定义为车辆当前朝向与正北方向夹角。）根据前面的数据可以推算出B点在车辆坐标系下的位置。具体关系图如下：

<div align="center">
<img src="https://i.imgur.com/s0nCJGM.png" title="Vehicle coordinate image"/>
</div>

如图，${\delta}_1$ 为A，B两点的夹角，可以由前面的方法计算得到，${\delta}_2$ 为车辆航向角，可以由GPS+IMU获取，B点在车辆坐标系下的 $x$ , $y$ 坐标为：

$$
\begin{align*}
  B_x = Dis_{AB} * cos({\delta}_2 - {\delta}_1 + 90) \\
  A_x = Dis_{AB} * sin({\delta}_2 - {\delta}_1 + 90)
\end{align*}
$$

## 实现代码(C++)

```c++
#include<cmath>

struct MapPoint
{
	double x;
	double y;
};

double gpsConvertMap(double lat1, double lng1, double lat2, doulbe lng2, double dir)
{
	double angle = getAngle(lat1, lng1, lat2, lng2);
	double radAngle = rad(dir - angle + 90);
	double distance = getDistance(lat1, lng1, lat2, lng2);


	MapPoint point;
	point.x = distance * cos(radAngle);
	point.y = distance * sin(radAngle);

	return point;
}
```

# 参考
- [使用GPS坐标来计算距离和方位角](https://johnnyqian.net/blog/gps-locator/)
- [根据两点经纬度坐标计算距离](https://blog.csdn.net/gatieme/article/details/45599581)