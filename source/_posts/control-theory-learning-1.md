---
title: Control theory learning (1)
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

Now we connect the system with their response in time domain. Given a sytem in state space model with their inital condition $x_0$ and input $u(t)$. Their response satisfy the following equations
$$
\begin{split}
y(t) & = Ce^{At}x_0 + \int_0^tCe^{A(t-\tau)}Bu(\tau)d\tau+Du(\tau) \quad \mathbb{(CT)}\\
y(t) & = Ce^{At}x_0 + \Sigma_{i=0}^{t-1}CA^{t-1-i}Bu(i)+Du(t) \quad \mathbb{(DT)}
\end{split}
$$

