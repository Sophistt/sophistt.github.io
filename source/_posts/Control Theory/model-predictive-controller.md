---
title: Model Predictive Controller
date: 2020-05-15 12:23:35
katex: true
tags: 
categories:
  - Study
  - Control Theory
---

**Model Predictive Control** (MPC) is an advanced method of process control that is used to control a process while satisfying a set of constraints. It has been in use in the process industries in chemical plants and oil refineries since the 1980s. In recent years it has also been used in power system balancing models and in power electronics. Model predictive controllers rely on dynamic models of the process, most often linear empirical models obtained by system identification. The main advantage of MPC is the fact that it allows the current timeslot to be optimized, while keeping future timeslots in account. This is achieved by optimizing a finite time-horizon, but only implementing the current timeslot and then optimizing again, repeatedly, thus differing from Linear-Quadratic Regulator (LQR). Also MPC has the ability to anticipate future events and can take control actions accordingly. PID controllers do not have this predictive ability. MPC is nearly universally implemented as a digital control, although there is research into achieving faster response times with specially designed analog circuitry.

# State Matrix

In order to use MPC, we have to model the plant by state variables. State variable models are basically time domain models where we are interested in the dynamics of some characterizing variables called state variables which along with the input represent the state of a system at a given time. 

The general structure of a state-space model of a $n^{th}$ order continuous time dynamical system with $m$ input is given by:

$$
\dot{x}(t) = Ax(t) + Bu(t) : \quad State \quad Equation \\
y(t) = Cx(t) + Du(t)       : \quad Output \quad Equation
\tag{1}
$$

Where $x(t)$ is the $n$ dimensional state vector, $u(t)$ is the $m$ dimensional input vector, $y(t)$ is the $p$ dimensional output vector and $A \in R^{n \times n}, B \in R^{n \times m}, C \in R^{p \times n}, D \in R^{p \times m}$

## Derivation

But how to get this **State Matrix** from differential equations? Consider a $n^{th}$ order differential equation, 

$$
\frac{d^ny}{dt^n} + a_1\frac{d^{n-1}y}{dt^{n-1}}+ ... + a_ny = u
$$

Define following variables,

$$
y  = x_1 \\
\frac{dy}{dt}  = x_2 \\
\vdots = \vdots \\
\frac{d^{n-1}y}{dt^{n-1}} = x_n \\
\frac{d^y}{dt^n} = -a_1x_{n-1} - a_2x_{n-2} - ... - a_nx_1 + u
$$

The n^{th} order differential equation may be written in the form of $n$ first order differential equation as,

$$
\dot{x}_1 = x_2 \\
\dot{x}_2 = x_3 \\
\vdots = \vdots \\
\dot{x_n} = -a_1x_{n-1} - a_2x_{n-2} - ... - a_nx_1 + u
$$

or in matrix forms as,

$$
\dot{x} = Ax + Bu
$$

where 

$$
A = 
\begin{bmatrix}
0 \quad 1 \quad 0 \quad ... \quad 0 \\
0 \quad 0 \quad 1 \quad ... \quad 0 \\
\vdots  \quad ... \quad \quad ... \quad \vdots \\
0 \quad 0 \quad 0 \quad ... \quad 1
\end{bmatrix} \quad B =
\begin{bmatrix}
0 \\
0 \\
\vdots \\
1
\end{bmatrix}
$$

The output can be one of states or a combination of many states. Since $y=x_1$,

$$
y = [1 \quad 0 \quad ... \quad 0]x
$$

## Solution

Solve the equation (1), we could get its solution as, 

$$
x(t) = e^{A(t-t_0)}x(t_0) + {\int}_{t_0}^t e^{A(t-\tau)}Bu(\tau)d\tau
$$

where, $e^{At}=\Phi(t)$ is known as the state transition matrix and $x(t_0)$ is the intial state of the system.

## Discretization

To be continued ...

# References

- [name](url)
- [name](url)