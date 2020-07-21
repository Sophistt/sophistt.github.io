---
title: Proportional-Integral-Derivative(PID) Controller
date: 2019-12-04  16:47:36
mathjax: true
tags:
categories: 
  - Study
  - Control Theory
---


A **proportional-integral-derivative controller** (PID controller) is a control loop mechanism employing feedback taht is widely used in industrial control systems and a variety of other applications requiring continously modulated control.

# Principle

A PID controller continuously calculates an error value $e(t)$ as the difference between a desired setpoint (SP) and a measured process variable (PV) and applies a correction based on proportional, integral, and derivate terms (denoted *P*, *I* and *D* respectively),
hence the name.

<div align="center">
  <img src="https://i.imgur.com/ci6iFtK.png" alt="Image of PID Controller"/><br>
</div>

## Positional PID Controller 

From the above image, we can get the equation of Postional PID controller:

<div align="center">
  <img src="https://i.imgur.com/LDFUNlL.png"/><br>
</div>

In this equation, $e(t)$ means the error between target output and current output; $K_P$ is the coefficient of the proportional process; $T_I$ is the coefficient of the integral process; $T_D$ is the coefficient of the derivative process.

However, in industrial control systems, the input and output values are discrete and we obtain them by sampling periodically by sensors. Therefore, the equation of PID controller should be transfromed into the discrete equation, in which $T$ is the sampling period.

<div align="center">
  <img src="https://i.imgur.com/izO8g6R.png"/><br>
</div>

Through the above equation, the control signal $u$ could be calculated by sampling the output $y$ of the discrete system each step. Therefore, such equation is named **Positional PID Controller**, since it calculates the input signals each step.

Unfortunately, **Positional PID controller** needs to save and compute all the errors between target output and actual output from the beginning till now, which cost numerous computing resources. Hence, **Incremental PID Controller** is introduced to resolve the problem.

## Incremental PID Controller

A new equation of positional PID controller in the $e_{k-1}$ moment is introduced,

<div align="center">
  <img src="https://i.imgur.com/FLoSUkB.png"/><br>
</div>

Use the equation (1) minus eqution (2), equation (3) could be obtained.

<div align="center">
  <img src="https://i.imgur.com/Rjk3kJs.png"/><br>
</div>

Through equation (3), we can calculate the increment of control signals only by saving past errors $e_{k-1}$, $e_{k-2}$.

# Bonus

So, how to choose the optimal value of parameters $K_P$, $T_D$ and $T_I$?

## *KP*

The parameter $K_P$ has an impact on the proportional process of PID controller and decide the tracking speed. However, if the plant is affected by an external load, the pure P Controller cannot reach the target output, which introduces the steady-state error.

## *TI*

In order to eliminate the steady-state error, integral process is introduced and this procedure depends on the parameter $T_I$. Although the integral process is able to eliminate the steady-state error, it brings the problem of **overshooting and oscillation**.

## *TD*

To resolve the problem of overshooting, derivative process use errors $e_k$ and $e_{k-1}$ to output the correction value.

## Adjust Parameters

Hence, we can optimize the parameters of PID Controller through the following processes,

### Common Method
- Set a small $K_P$ and an infinite $T_I$ with $T_D$ eqaulling to 0. Then gradually increase $K_P$ until getting an opportune tracking speed and a satisfactory quarter attenuation transition curve.
- If the steady-state error occurs, increase the effect of the integral process by decrease the parameter $T_I$  until we think the integral process is enough, meanwhile, $K_P$ should be set to $5/6K_P$.
- If the problem of overshooting and oscillation occurs, set $T_D = (1/3 \sim 1/4)T_I$ and increase it, which can miligate the problem but it will slow down the tracking speed.

### Critical Ratio Method
- Set a small $K_P$ and an infinite $T_I$ with $T_D$ eqaulling to 0. Then gradually increase $K_P$ until getting an equal amplitude oscillation, then record the critical coefficient $K_u$ and the critical period of ascillation $T_u$.
- Calculate the coefficients of $K_P, T_I, T_D$ according to empirical formula.
- Set coefficients of PID controller to the computed parameter and optimize them if necessary. 

<div align="center">
  <img src="https://i.imgur.com/ErCxwpD.png" alt="Image of Empirical Formula"/><br>
</div>

# References

- [PID Controller in Wikipedia](https://en.wikipedia.org/wiki/PID_controller)
