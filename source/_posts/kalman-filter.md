---
title: Kalman Filter
date: 2019-07-24 16:02:26
mathjax: true
tags: 
categories: 
  - Study
  - Math
---

**Kalman filter** is a technology to achieve *Bayes filter*. It's an **optimal linear state estimation method**  (eqauls to **optimal linear filter under the minimum mean square error criterion**), which seeks the state vector that best fits the observed data by mathematical methods. 

In localization and navigation of robots, kalman filter is the most commonly used method to estimante the robot's status, as  it solve the problem recursively and only the estimation value in the last sampling period and the measurement in the current sampling period are needed, which saves memory and calculating time.

# Principle

## Prerequisite

Before using kalman filter, 3 prerequisites should be statisfied:
- State transition probability must be the linear function fo parameters with random Gaussian noise
- Measurement probabilty also has to contain Gaussian component
- Initial confidence is normally distributed

## Problem Description

Define a status vector with random-Gaussian-noise parameters:

$$
\begin{align*}
  x_t  = A_tx_{t-1} + B_tu_t + \epsilon_t
\end{align*}
$$

In this equation, $x_t$ and $x_{t-1}$ represents the status vector ($n \times 1$) in moment $t$ and $t-1$ respectively; $A$ is the state transition matrix in $n x n$ dimension; $u_t$ represents the control  vector ($m \times 1$) on moment $t$; $B_t$ is an optional gain matrix of control vector in $n \times m$ dimension; $\epsilon_t$ is a random Gaussian vector that represents the uncertainty introduced by status transition.

Note: all vectors are column vectors in the following form:

$$
\begin{align*}
  x_t =
  \begin{bmatrix}
  x_{1, t} \\ x_{2, t} \\ . \\ . \\ x_{n, t}
  \end{bmatrix} \quad

  and \quad 

  u_t =
  \begin{bmatrix}
  u_{1, t} \\ u_{2, t} \\ . \\ . \\ u_{m, t}
  \end{bmatrix}
\end{align*}
$$

Define another observation vector ($k * 1$):

$$
\begin{align*}
  z_t  = H_tx_{t} + \delta_t
\end{align*}
$$

In this equation, $H_t$ is the gain matrix in $k * n$ dimension; $\delta_t$ is the observation noise.

## Example

Give an example to explain the above equations. Suppose that we are tracking a car in uniform motion through GPS and other sensors. In general, we would like to get information about its position and velocity (or orientation) when we observe a vehicle.
Therefore, status vector could be defined as $x_t = (x_t, y_t, v^x, v^y)^T$. Simultanenously, it's known that in uniform motion the control vector is $u_t = (v^x, v^y)^T$.

As a result, the matrix $A$ and $B$ are defined as follow:

$$
\begin{align*}
  A = 
  \begin{bmatrix}
  1 \quad 0 \quad 0 \quad 0 \\
  0 \quad 1 \quad 0 \quad 0 \\
  0 \quad 0 \quad 1 \quad 0 \\
  0 \quad 0 \quad 0 \quad 1 \\
  \end{bmatrix}

  \quad and \quad

  B = 
  \begin{bmatrix}
  dt \quad 0 \\
  0 \quad dt \\
  0 \quad 0 \\
  0 \quad 0 \\
  \end{bmatrix}

\end{align*}
$$

Finally, we can get the status equation:

$$
\begin{align*}
  x_t  = A_tx_{t-1} + B_tu_t + \epsilon_t

  \Rightarrow

  \begin{bmatrix}
  x_t \\ y_t \\ v^x \\ v^y
  \end{bmatrix} =  

  \begin{bmatrix}
  1 \quad 0 \quad 0 \quad 0 \\
  0 \quad 1 \quad 0 \quad 0 \\
  0 \quad 0 \quad 1 \quad 0 \\
  0 \quad 0 \quad 0 \quad 1 \\
  \end{bmatrix}
  
  \begin{bmatrix}
  x_{t-1} \\ y_{t-1} \\ v^x \\ v^y
  \end{bmatrix} +

  \begin{bmatrix}
  dt \quad 0 \\
  0 \quad dt \\
  0 \quad 0 \\
  0 \quad 0 \\
  \end{bmatrix}

  \begin{bmatrix}
  v^x \\ v^y
  \end{bmatrix} + \epsilon_t = 


  \begin{bmatrix}
  x_{t-1} + v^x * dt \\ y_{t-1} + v^y * dt \\ v^x \\ v^y
  \end{bmatrix} + \epsilon_t  

\end{align*}
$$

Similarly, $H$ is also required to be defined. As we can get the information about vehicle's position from GPS sensor, the observation vector is defined as $z_t = (x_t, y_t)^T$.
$H$ is a $k * n$ dimensional matrix, so that we can get the equation as follow:

$$
\begin{align*}
  z_t  = H_tx_{t} + \delta_t

  \Rightarrow

  \begin{bmatrix}
  1 \quad 0 \quad 0 \quad 0 \\
  0 \quad 1 \quad 0 \quad 0 \\
  \end{bmatrix} 

  \begin{bmatrix}
  x_t \\ y_t \\ v^x \\ v^y
  \end{bmatrix} + \delta_t =

  \begin{bmatrix}
  x_t \\ y_t 
  \end{bmatrix} + \delta_t
  

\end{align*}
$$

# Algorithm

After defining $x_t$, $z_t$, $A$ and $H$, we can use kalman fitler algorithm to estimate $x_t$.

## Prediction

$$
\begin{align*}
  \hat{x}^-_t = A\hat{x_{t-1}} + Bu_t \\
  \hat{P}^-_t = A\hat{P_{t-1}}A^T + Q
\end{align*}
$$

## Update 


$$
\begin{align*}
  K_t = \frac{\hat{P}^-_tH^T}{H^T\hat{P}^-_tH^T + R} \\
  \hat{x}_t = \hat{x}^-_t + K_t(z_t - H\hat{x}^-_t) \\
  \hat{P}_t = (I - K_tH)\hat{P}^-_t
\end{align*}
$$

Explanaiton of parameters in these equations:
- $\hat{x}^-_t$: the priori estimated value of status vector at moment $t$ <br>
- $\hat{x}_t$: the posterior estimated value of status vector at moment $t$, the result of kalman filter
- $\hat{x}_{t-1}$: the posterior estimated value of status vector at moment $t-1$
- $A$: status transition matrix
- $B$: an optional gain matrix of control vector
- $u_{t}$: control vector at moment $t$
- $\hat{P}^-_t$: the priori estimated covariance at moment $t$
- $\hat{P}_{t-1}$: the posterior estimated covariance at moment $t$, a part of result of filter
- $K_t$: kalman gain coefficient, intermediate variable of equation
- <font color="red">$Q$</font><font color="black">: covariance of process noise that represents the error between status transition matrix and actual process.It's difficult to seek the best value of $Q$, 
	but it follows some priciples: In general, $Q$ is diagonal matrix and the values are small for prompt convergence.</font>
- <font color="red">$R$</font><font color="black">: covariance of measured noise that relates to measured instruments, the smaller of this value, the higher of the convergence speed</font>

# Expansion

Unfortunately, **kalman fitler** only can be applied to problems with linear status transition matrix and measurement, which confine it in simple robotic problem. Therefore, **Extended Kalman Filter** is introduced to break the limitation.

## Reality

Essentially, the linear prediction and measurement in kalman filter are replaced by no-linear prediction and measurement in extended kalman filter. As the result, $Ax_{t-1} + Bu_t$ is replaced by Jacobi Matrix $g(x_{t-1}, u_t)$ and $Hx_t$ is replaced by Jacobi Matrix $h(x_t)$ .

| KF | EKF | 
| :----: | :-----: | 
| $A\hat{x_{t-1}} + Bu_t$ | $g(x_{t-1}, u_t)$ | 
| $H\hat{x}^-_t$ | $h(\hat{x}^-_t)$ | 

## Compute Jacobi Matrix

Take the above vehicle as an example:

$$
\begin{align*}
  J_{A} = 
  \begin{bmatrix}
  \frac{dx}{dx} \quad \frac{dx}{dy} \quad \frac{dx}{dv^x} \quad \frac{dx}{dv^y} \\
  \frac{dy}{dx} \quad \frac{dy}{dy} \quad \frac{dy}{dv^x} \quad \frac{dy}{dv^y} \\
  \frac{dv^x}{dx} \quad \frac{dv^x}{dy} \quad \frac{dv^x}{dv^x} \quad \frac{dv^x}{dv^y} \\
  \frac{dv^y}{dx} \quad \frac{dv^y}{dy} \quad \frac{dv^y}{dv^x} \quad \frac{dv^y}{dv^y} 
  \end{bmatrix} =

  \begin{bmatrix}
  1 \quad 0 \quad dt \quad 0 \\
  0 \quad 1 \quad 0 \quad dt \\
  0 \quad 0 \quad 1 \quad 0 \\
  0 \quad 0 \quad 0 \quad 1 \\
  \end{bmatrix}
	
  \\ \\ 

  J_{H} = 
  \begin{bmatrix}
  \frac{dx}{dx} \quad \frac{dx}{dy} \quad \frac{dx}{dv^x} \quad \frac{dx}{dv^y} \\
  \frac{dy}{dx} \quad \frac{dy}{dy} \quad \frac{dy}{dv^x} \quad \frac{dy}{dv^y} \\
  \end{bmatrix} =
  
  \begin{bmatrix}
  1 \quad 0 \quad 0 \quad 0 \\
  0 \quad 1 \quad 0 \quad 0 \\
  \end{bmatrix}
\end{align*}
$$

## Prediction and Update 

$$
\begin{align*}
  \hat{x}^-_t = g(x_{t-1}, u_t) \\
  \hat{P}^-_t = A\hat{P_{t-1}}A^T + Q \\
  K_t = \frac{\hat{P}^-_tH^T}{H^T\hat{P}^-_tH^T + R} \\
  \hat{x}_t = \hat{x}^-_t + K_t(z_t - h(\hat{x}^-_t)) \\
  \hat{P}_t = (I - K_tH)\hat{P}^-_t
\end{align*}
$$

# Codes
- [kalman_filter.py](https://github.com/Sophistt/Small_Projects/blob/master/kalman_filter/kalman_filter.py)

# References

- [Probabilistic Robotics](http://www.probabilistic-robotics.org/)
- [卡尔曼滤波的理解以及参数调整](https://blog.csdn.net/u013453604/article/details/50301477)

