---
title: State Space Model
date: 2020-11-8 10:30:00
katex: true
tags: Control Theory
categories: Study
---

Given an arbitrary system, we can use various methods to describe it such as **Transfer Function**, **Differential Equations** and **State Space Model**. Here we mainly foucos on **State Space Model** and solve following questions,

- What is State Space Model?
- How to contruct the State Space Model of a specified plant?
- How to convert State Space Model to other description methods like Transfer Function?

## What is State Space Model?

State space model (SSM) refers to a class of probabilistic graphical model (Koller and Friedman, 2009) that describes the probabilistic dependence between the latent state variable and the observed measurement. The state or the measurement can be either continuous or discrete. 

### Continuous Time System

Here we only discuss the simple case that the system is linear and time-invariant with $p$ inputs, $q$ outputs and $n$ state variables. The SSM of the plant is written in the following form,

$$
\dot{x}(t) = Ax(t) + Bu(t) \\
y(t) = Cx(t) + Du(t)
$$

where,

- $x(\cdot)$ is called the "state vector", $x(t) \in \mathbb{R}^n$;
- $y(\cdot )$ is called the "output vector",  $y(t)\in \mathbb {R} ^{q}$;
- $u(\cdot )$ is called the "input (or control) vector", $u(t)\in \mathbb {R} ^{p}$;
- ${A(\cdot )}$ is the "state (or system) matrix",  $A(\cdot )=n\times n$,
- ${B(\cdot )}$ is the "input matrix",  ${B (\cdot )=n\times p}$,
- ${C(\cdot )}$ is the "output matrix",  ${C (\cdot )=q\times n}$,
- ${D(\cdot )}$ is the "feedthrough (or feedforward) matrix" (in cases where the system model does not have a direct feedthrough, ${D(\cdot )}$ is the zero matrix),  ${\displaystyle \operatorname {dim} [\mathbf {D} (\cdot )]=q\times p}$,
- $\dot{x}(t) = \frac{d}{dt}x(t)$

### Discrete Time System

In discrete time system, we define similar equations in the following form,

$$
x(t+1) = Ax(t) + Bu(t) \\
y(t) = Cx(t) + Du(t)
$$

### What's the meaning of such parameters?

Matrices $A, B, C, D$ have its own features and by analyzing them we can understand characteristic features of the plant. \

For example, matrix $A$ represents the stability of the system. If and only if all eigenvalues of $A$ have neagtive or zero real part and all **Jordan blocks** corresponding to eigenvalues with zero real part are $1 \times 1$, the plant is **Lyapunov Stable**.

More detals about it will be discussed in next posts.

## How to construct a SSM of a specified plant?

Consider the classic mass-spring-damper system as shown below. Two motors apply forces $F_1$ and $F_2$ to the mass $m_1$ and $m_2$ respectively, resulting translations $x_1$ and $x_2$, 

<a href="https://imgur.com/bO4WjRx"><img src="https://i.imgur.com/bO4WjRx.png" title="source: imgur.com" /></a>

### Construct differential equations

By analyzing the system, we get its differential equations in the following form,

$$
m_2 \ddot{x}_2 + d(\dot{x}_2 - \dot{x}_1) + k(x_2 - x_1) = F_2 \\
m_1 \ddot{x}_1 + d(\dot{x}_1 - \dot{x}_2) + k(x_1 - x_2) = F_1
$$

### Define state vecotr $x(t)$

{% note info %}
Definitions of $x(t)$ are not unique. Using different defitions bring different matrices $A, B, C, D$.
{% endnote%}

Here we define,

$$
X = 
\begin{bmatrix}
x_1(t) \\ x_2(t) \\ x_3(t) \\ x_4(t) 
\end{bmatrix} = 
\begin{bmatrix}
x_1 \\ \dot{x}_1 \\ x_2 \\ \dot{x}_2
\end{bmatrix}, \quad
\dot{X} = 
\begin{bmatrix}
\dot{x}_1(t) \\ \dot{x}_2(t) \\ \dot{x}_3(t) \\ \dot{x}_4(t) 
\end{bmatrix} = 
\begin{bmatrix}
x_2(t) \\ \ddot{x}_1 \\ x_4(t) \\ \ddot{x}_2
\end{bmatrix}
$$

### Sort out differential equations

from differential equations we get, 

$$
\ddot{x}_2 = \frac{1}{m_2} [F_2 - d(\dot{x}_2 - \dot{x}_1) - k(x_2 - x_1)] \\
\ddot{x}_1 = \frac{1}{m_1} [F_1 - d(\dot{x}_1 - \dot{x}_2) - k(x_1 - x_2)] 
$$

### Turn into matrix form

Hence we get, 

$$
\dot{X}(t) = 
\begin{bmatrix}
0 & 1 & 0 & 0 \\
-\frac{k}{m_1} & -\frac{d}{m_1} & \frac{k}{m_1} & \frac{d}{m_1} \\
0 & 0 &  0 & 1 \\
\frac{k}{m_2} & \frac{d}{m_2} & -\frac{k}{m_2} & -\frac{d}{m_2}
\end{bmatrix} X(t) + 
\begin{bmatrix}
0 & 0 \\
\frac{1}{m_1} & 0 \\
0 & 0 \\
0 & \frac{1}{m_2}
\end{bmatrix} U(t) \\
Y(t) = 
\begin{bmatrix}
1 & 0 & 0 & 0 \\
0 & 0 & 1 & 0
\end{bmatrix} X(t) + 
\begin{bmatrix}
0 & 0 \\
0 & 0
\end{bmatrix} U(t)
$$

Note that $U(t) = [F_1 \quad F_2]^\top$

## How to convert SSM to transfer function?

For a specified plant, we have various SSM since we can define different state vectors. However, there only exists an unique transfer function $G(s)$. Besides, if we get a SSM of a plant, we can easily find its tranfer function through the following equation,

### Continuous Time System

$$
G(s) = C(sI-A)^{-1}B+D
$$

### Discrete Time System

$$
G(z) = C(zI-A)^{-1}B+D
$$