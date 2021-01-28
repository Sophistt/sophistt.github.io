---
title: Fundamental Concepts (Control Theory Learning 1)
date: 2021-01-28 13:50:50
mathjax: true
tags: Control Theory
categories: Study
---


# Fundamental Concepts

## State Space Model

Given a system, we have various methods to define its properties, including **State Space Model**,
**Transfer function**, **Odinary differential equations** and etc. Different description methods can be interchanged.

### Example

Given a CT(*Continuous-time*) or DT(*Discrete-time*) system,

$$
\begin{split}
&\dot{x}(t) = Ax(t) + Bu(t) \quad| \quad x(t+1) &= Ax(t)+Bu(t) \\
& y(t) = Cx(t) + Du(t)   \quad |\quad \quad \quad y(t)& = Cx(t)+Du(t)
\end{split}
$$

Their transfer functions are,
$$
\begin{split}
G(s) & = C(sI-A)^{-1}B+D, \quad \mathbb{Laplace} \\
G(z) & = C(zI-A)^{-1}B+D, \quad \mathbb{z-transfrom}
\end{split}
$$
Notice that transfer fucntion descirbes properties of the sytem in frequency domain.

Details about state space model could be seen at this link: [State Space Model](https://sophistt.github.io/2020/11/08/state-space-model/)

## Response of the system

Now we connect the system with their response in time domain. Given a sytem in state space model with their inital condition $x_0$ and input $u(t)$. Their **general response** satisfy the following equations
$$
\begin{split}
y(t) & = Ce^{At}x_0 + \int_0^tCe^{A(t-\tau)}Bu(\tau)d\tau+Du(\tau) \quad \mathbb{(CT)}\\
y(t) & = Ce^{At}x_0 + \Sigma_{i=0}^{t-1}CA^{t-1-i}Bu(i)+Du(t) \quad \mathbb{(DT)}
\end{split}
$$

### Impulse reponse

Impulse reponse generally refers to the output of the system when the input is a unit pulse function. In other words, the initial condition of the sytem $x_0=0$ and input $u(t)=\delta(t)$.
$$
y(t) = Ce^{At}B+D\delta(t) \quad | \quad y(t)=CA^{t-1}B
$$

### A simple method to compute $e^{At}$

Given a state space model, if we would like to know its response in time domain. It's needed to calculate $e^{At}$. A simple method to calculate is computing $(sI-A)^{-1}$ or $z(zI-A)^{-1}$ then use inverse Laplace or Z-transform.



## Canonical Form

Given a transfer function $G(s)$, we can use Canonical Form to derive its state space model.
$$
\begin{split}
G(s) & = \frac{b_0s^n+b_1s^{n-1}+...+b_{n-1}s+b_n}{s^n+a_1s^{n-1}+...+a_{n-1}s+a_n} \\
& = b_0 + \frac{c_1s^{n-1}+...+c_{n-1}s+c_n}{s^n+a_1s^{n-1}+...+a_{n-1}s+a_n}
\end{split}
$$
with $c_i=b_i-b_0a_i, i= 1, 2,..., n$

Then we can get the controllability Canonical Form
$$
\begin{split}
A &=
\begin{bmatrix}
0 & 1 & 0 & ... & 0\\
0 & 0 & 1 & ... & 0 \\
\vdots & \vdots & \vdots & \vdots & \vdots \\
0 & 0 & 0 & ... & 1 \\
-a_n & -a_{n-1} & -a_{n-2} & ... & -a_1
\end{bmatrix} \quad
&B=
\begin{bmatrix}
0 \\ 0 \\ \vdots \\ 0 \\ 1
\end{bmatrix} \\
C &= \begin{bmatrix}
c_n  & c_{n-1} & ... & c_2 & c_1
\end{bmatrix} \quad
&D  = [b_0]
\end{split}
$$
