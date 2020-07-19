---
title: Linear-Quadratic-Regulator(LQR) Controller
date: 2020-05-02 12:00:16
mathjax: true
tags: 
categories:
  - Study
  - Control Theory
---

The theory of **optimal control** is concerned with **operating a dynamic system at minimum cost**. The case where the system dynamics are described by a set of linear differential equations and the cost is described by a quadratic function is called the LQ problem. One of the main results in the theory is that the solution is provided by the **linear–quadratic regulator (LQR)**, a feedback controller whose equations are given below.

# General Description

{% note info %}
**From Wikipedia**
The settings of a (regulating) controller governing either a machine or process (like an airplane or chemical reactor) are found by using a mathematical algorithm that minimizes a cost function with weighting factors supplied by a human (engineer). The **cost function is often defined as a sum of the deviations of key measurements, like altitude or process temperature, from their desired values.** The algorithm thus finds those controller settings that minimize undesired deviations. The **magnitude of the control action itself may also be included in the cost function.**
The LQR algorithm <font color="green">reduces the amount of work</font> done by the control systems engineer to optimize the controller. However,  **the engineer still needs to specify the cost function parameters, and compare the results with the specified design goals.** Often this means that controller construction will be an iterative process in which the engineer judges the "optimal" controllers produced through simulation and then adjusts the parameters to produce a controller more consistent with design goals.
The **LQR algorithm is essentially an automated way of finding an appropriate state-feedback controller.** As such, it is not uncommon for control engineers to prefer alternative methods, like full state feedback, also known as pole placement, in which there is a clearer relationship between controller parameters and controller behavior. <font color="red">Difficulty in finding the right weighting factors limits the application of the LQR based controller synthesis.</font>
{% endnote %}

## Method

Here only **finite-horizon, discrete-time LQR problem** will be discussed

## Goal

In this problem, the discrete-time linear system described by: 

$$
x_{k+1} = Ax_k + Bu_k
$$

Its performance could be defined by the following cost function:

$$
J = {\sum}^T_{t=1}(x^T_tQx_t + u^T_tRu_t + 2x^T_tNu_k)
$$

*This equation could be simplified by removing the last Gaussian Coefficient.:*

$$
J = {\sum}^T_{t=1}(x^T_tQx_t + u^T_tRu_t)
$$

## Solution

But how to minimize the cost function $J$? Algebraic Riccati equation provides a common method to resolve this problem, which is named **DARE** in discrete-time problem.

From terminal condition $P_T=Q, P_t$ is found iteratively backwards in time by the dynamic Riccati equation:

$$
P_{t−1}=A^TP_tA − A^TP_tB(B^TP_tB+R)^{−1} B^T P_t A+Q
$$

The steady-state characterization of $P$, relevant for the infinite-horizon problem in which $T$ goes to infinity, can be found by iterating the dynamic equation repeatedly until it converges; then $P$ is characterized by removing the time subscripts from the dynamic equation.

The optimal control sequence minimizing the performance index is given by:

$$
u_t^∗=−Kx_{t−1}
$$

Where:

$$
K=(R+B^T PB)^{−1} B^T PA
$$

## Conclusion

Therefore, the whole process of LQR algorithm could be concluded as,
1. Set $P_t$  equal to $Q$
2. Iterate $P_t$  through Algebraic Riccati equation
3. Terminate iteration of $P$ while $P_{t−1}  −P_t$  is small enough and calculate feedback matrix $K$
4. Calculate the optimal control signal $u^∗$  through $K$

## Application

[Analysis of Apollo control algorithm](https://zhuanlan.zhihu.com/p/72702387)
In the field of autonomous driving, how to track the planning path well is a really core problem. Geometry algorithm such as **Pure Pursuit Algorithm, Stanley Algorithm** is only suitable for low-speed and simple conditions.  In city working condition, modern advanced control theories such as **Model Predictive Control** and **Linear-Quadratic Regulator control** are more appropriate in these conditions.

Here we will discuss how to use LQR Algorithm to track the planning trajectory of self-driving vehicles.

<div align="center">
    <img src="https://i.imgur.com/bcIh4nV.png" width="20%" /><br>
    <font size="4" color="red">Path tracking</font>
</div>

If the state $x=[s, \dot{s}, \theta, \dot{\theta}]$ of the self-driving vehicle and the planned trajectory (black points) is known, the state of the vehicle in the soon future (showed as the red points) could be calculated through a sequence fo given control signals u according to the state model $x_{t+1}=Ax_t+Bu_t.$

We can use a quadratic cost function to evaluate the performance of the vehicle as followed,

$$
J = \sum_{t=1}^T((x_t - x_t^*)^TQ(x_t-x_t^*) + u^T_tRu_t)
$$

Where $x_t^∗$  represents the sequences of desired states in planned trajectory and $x_t$  represents the predicted points. We can minimize this objective by linearizing the dynamics around the target trajectory and applying LQR algorithm to the resulting linearized system. This optimization is done in an online fashion, each time a new control command will be calculated and sent to controller of the vehicle. 

However, in the condition without considering the rate of vehicle's heading angle, the **lateral deviation** $e$ and **heading deviation** $θ_e$  are relevant to the curvature of the road. LQR algorithm can leads the system to the stable state, but it cannot eliminate the steady-state error from the curvature of the road. Therefore, **a feedforward control signal** $δ_{ff}$  is introduced to suppress the steady-state error. In simplified cases, $δ_{ff}=arctan⁡(L ∗k)$, where $L$ is wheel base of the vehicle and k is the curvature of the road.

Therefore, 

$$
δ=δ_{fb}+δ_{ff}=−Kx_{t−1}+arctan⁡(L ∗k)
$$

# Bonus

But how to define the suitable matrices A, B, Q, R in different systems? The following gives detailed steps to define these matrices as an example. (from Apollo)

## Example

In autonomous driving, LQR Controller can be used for lateral steering wheel control. Therefore, dynamic analysis is required to build a vehicle dynamic model. 

## Dynamic analysis 

Firstly, we should analyze the forces of vehicles.

<div align="center">
    <img src="https://i.imgur.com/mlCMAbP.png" width="60%" /><br>
    <font size="4" color="red">Vehicle Dynamics</font>
</div>

As for lateral steering wheel control, we should pay more attention on **lateral forces**. 

Analyze the forces on the front and read wheels of the vehicle, 

$$
ma_y=F_{yf}+F_{yr}    (1)
$$

According to momenta balance of the vehicle,

$$
I_z ψ ̈=l_f F_{yf}−l_r F_{yr}   (2)
$$

In equations (1), (2), the car quality $m$, monenta of inertia $I_z$, distance from front or rear axle to center $l_f$, $l_r$ are measurable. To resolve the equations, lateral acceleration $a_y$  and lateral forces $F_{yf}$, $F_{yr}$  are needed.

Lateral acceleration $a_y$ can be decomposed into acceleration caused by lateral displacement and centripetal acceleration.

$$
a_y=y ̈+V_x ψ ̇   (3)
$$

Where $y$ is lateral displacement, $ψ$ is heading angle and $V_x$  is longitudinal speed.

Lateral forces $F_{yf}$  and $F_{yr}$  can be deduced by the below equations,

$$
F_{yf}=2C_{af} (δ−θ_{Vf} )  (4) \\
F_{yr}=2C_{ar} (−θ_{Vr})   (5)
$$

Where $C_{af}$  and $C_{ar}$ are **lateral stiffness** of front and rear wheels respectively, $δ$ is front wheel angle and $θ_{Vf}$  and $θ_{Vr}$ are **velocity deviation angle** of front and rear wheels.

<div align="center">
    <img src="https://i.imgur.com/f1tGIac.png" width="60%" /><br>
</div>

From the image above, we can get the below equations, 

$$
tan⁡(θ_{Vf})=(V_y+l_f ψ ̇)/V_x  \\
tan⁡(θ_{Vr})=(V_y−l_r ψ ̇)/V_x 
$$

In assumption of small angle, 

$$
θ_{Vf}=(V_y+l_f ψ ̇)/V_x  \\
θ_{Vr}=(V_y−l_r ψ ̇)/V_x 
$$

Back to formula (1) and (2), we can get,

$$
y ̈=a_y=−\frac{2C_{af}+2C_{ar}}{mV_x}y ̇+ (−V_x−\frac{2C_{af} l_f−2C_{ar} l_r}{mV_x} ψ ̇+ \frac{2C_{af}}{m} δ  (6)  \\
ψ ̈=−\frac{2l_f C_{af}−2l_r C_{ar}}{I_z V_x}y ̇+ (−\frac{2l_f^2 C_{af}+2l_r^2 C_{ar}}{I_z V_x}) ψ ̇+ \frac{2l_f C_{af}}{I_z}  δ  (7)
$$

To get the state space expression of vehicle motion, we introduce lateral error $e_1$ and heading angle error $e_2$. Hence we get the below equations,

<div align="center">
    <img src="https://i.imgur.com/0Qm7f1d.png" /><br>
</div>

In the assumption of uniform motion, a further deduction is,

<div align="center">
    <img src="https://i.imgur.com/wikvooY.png" /><br>
</div>

Similarly,

<div align="center">
    <img src="https://i.imgur.com/hqMxWKH.png" /><br>
</div>

And back to the equations (6) and (7), we can get the equations based on $e_1$ and $e_2$,

<div align="center">
    <img src="https://i.imgur.com/E7lxHPm.png" /><br>
</div>

Finally, change it to state space expression and we get the vehicle dynamics model expression.

<div align="center">
    <img src="https://i.imgur.com/chncUWK.png" /><br>
</div>

Set

<div align="center">
    <img src="https://i.imgur.com/9ysTDUl.png" /><br>
</div>

The final state expression is 

$$
\frac{dx}{dt} = Ax + Bu + C
$$

In this equation, lateral stiffness of front wheel Caf and rear wheel $C_{ar}$, car quality $m$, momenta of inertia $I_z$, distance from front or rear axle to center $l_f$, $l_r$ are constant.

Lateral error $e_1$, heading angle error $e_2$, longitudinal speed $V_x$ and front wheel angle $δ$ are measurable.

In this way, the target trajectory can be tracked by the corresponding algorithm using this model by updating the value measured by sensors every calculation cycle.

## Discretization

However, in practical applications, continuous model is not suitable in most cases. Hence we need a discrete model for calculation. 

$$
x((k+1)T)=A_D x(kT)+B_D u(kT)+C_D
$$

Where,

$$
A_D = \frac{e^{\frac{AT}{2}}}{e^{-\frac{AT}{2}}} = \frac{I + \frac{AT}{2}}{I - \frac{AT}{2}} = (I - \frac{AT}{2})^{-1}(I + \frac{AT}{2})  \\
B_D = \int_0^T e ^{At}dtB = TB \\ 
C_D = TC
$$

# References

- [Linera-quadratic regulator](https://en.wikipedia.org/wiki/Linear%E2%80%93quadratic_regulator)
- [Apollo控制算法LQR分析（二）](https://zhuanlan.zhihu.com/p/72702387)
