---
title: 动态系统 (System Dynamics)
date: 2020-08-28 22:40:00
katex: true
tags: System & Control
categories: Study
---


System Dynamics 研究的究竟是什么？

与单纯的Dynamics（动力学）的含义不同，system dynamics 研究是的系统的动态特性，和 static system 相对应。要理解系统的动态特性，首先要从静态系统开始阐述。

## 静态系统

以电路系统为例，如果电路仅为一个电阻**R**，那么在受到施加在该电阻上的电压**V**时，电路上瞬时产生电流**I**，并有熟知的公式
$$
I = \frac{V}{R}
$$
此简单的系统我们称之为静态系统，因为其变化是瞬时的，当电阻上的电压变化时，电流也随之变化。



## 动态系统

假如在电路中引入动态元件电感器（或电容器），那么因为该元件的动态特性，当电路中的电源电压发生变化时，电路中的电流**I**将不是瞬时发生变化，而是有一个渐变的过程，该过程无法用简单的代数方程表示，而是需要用**微分方程**来表示：
$$
L\frac{dI}{dt} + RI(t) = v(t)
$$
其中$v(t)$随时间变化的电源电压。

因此，**System Dynamics**研究的就是系统的动态特性，系统的某个条件发生变化时，系统在瞬时的变化是怎么样的，最终会达到什么样的状态（稳态）。在现实生活里，基本所有的系统都有自己的动态特性，但是在研究某个系统的时候，如果不关心其动态特性，可以将其化简为静态系统。（**只关心条件变化所带来的最终结果，不关心条件变化后从当前状态到达最终状态中间的过程变化。**）

**举例**

以直流电机为例，当将其简化为静态系统时，其输出力矩随着电压的变化而瞬时变化。

但在实际应用上，施加在直流电机上的电压发生变化时，其输出力矩并不是瞬时发生变化的，从初始位置到达新的位置需要一段时间，且变化的过程中伴随着震荡。

## 微分方程

当谈及动态系统的时候，一定涉及的概念便是微分方程，微分方程又可以分为**free differential equtions**和**forced differential equations**。

### Free differential equations

$$
m\ddot{x}+c\dot{x}+x=0
$$

方程的微分方程的右边为0时即**free differential equations**。

该微分方程的通解为：
$$
x(t)=e^{-{\delta}t}(Acosw_dt+Bsinw_dt)
$$
在此不讨论具体如何求解微分方程，只说明该通解中各部分的含义：

- ${\delta}$: 系统稳定性的指标，当$-{\delta}<0$时，意味着系统是稳定的，因为随着时间$t$的增加，$e^-{\delta}t$越来越小
- $\omega_d$：该指标代表系统在变化过程中震荡的频率

### Forced differential equaitons

<a href="https://imgur.com/1qJWpqV"><img src="https://i.imgur.com/1qJWpqV.png" alt="from System Dynamics and Control: Module 3 - Mathematical Modeling Part I" /></a>

可以看到，当方程组的右边不等于0时，该系统再是自由系统，因此需要使用**Forced differential eqautions**来描述。

此方程的解为：
$$
x(t) = x_h(t) + x_p(t)
$$
其中

- $x_h(t)$是方程的通解，为$m\ddot{x}+c\dot{x}+x=0$的解
- $x_p(t)$是方程的特解，与$F(t)$具有同样的形式

**举例**

求解：
$$
\ddot{x}+8\dot{x}+25x = 2t
$$
由原式可得：
$$
\lambda^2+8\lambda=25=0
$$
因此，有：
$$
\lambda_{1,2}=-4\pm3j
$$
所以有解的形式为：
$$
x(t)=e^{-4t}(Acos3t+Bsin3t)+at+b
$$
这意味着，当通解的部分随解时间增加而逐渐消失后，特解的部分仍然在起作用（稳态结果）。



# References

- [Systems Dynamics and Control: Module 2 - Introduction to Modeling](https://www.youtube.com/watch?v=vwso-xHLNGc&t=1056s)









