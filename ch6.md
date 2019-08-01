---
layout: post
mathjax: true
title: 'Chapter 6: Inverse Kinematics'
description: Modern Robotics Course Notes
is_project_page: false
---

<p style="text-align:center;">
<button type="button" onclick="window.location.href='index.html';">Homepage</button>
<span style="float:left;"><button type="button" onclick="window.location.href='ch5.html';">Previous</button></span>
<span style="float:right;"><button type="button" onclick="window.location.href='ch7.html';">Next</button></span>
</p>


## Analytic Inverse Kinematics and Numerical Inverse Kinematics

These are two common solutions for inverse kinematics problem. Analytic solution depends on the structure of the robot. They can be easily obtained from some popluar robot structures like PUMA and Stanford robot arms (both are discussed in the book). However, for a more general purpose, numerical method is a better idea. Here, the numerical method is adapted from Newton-Raphson method.

## Newton-Raphson Iterative Algorithm

> ... **Newton's method**, also known as **Newton-Raphson method**, ..., is a root-finding algorithm which produces successively better approximations to the roots (or zeros) of a real-valued function. <sup>[1]</sup>

For an iteration method, as well as recursion, I think the key issue is to find out the relation between two iterations. In the case of Newton-Raphson method, this relation is obtained by applying Taylor expansion to the target function and truncate it at first order.

## Numerical Inverse Kinematics Problem

### The Case of Minimal Set of Coordinates

If we set the forward kinematics $f(\theta)$ as target funtion, in the inverse kinematics problem, instead of finding its root, we want to find the $\theta_d$ that makes $f(\theta_d) = x_d$, which is not a root-finding problem. To apply Newton-Raphson method, we design a new function $g(\theta) = x_d - f(\theta)$ to make it a root-finding problem.

Similarly, we can get the relation between two iterations by applying Taylor expansion. Then, we can get the equation which I think is the most important one to understand numerical inverse kinematics problem:

$$
J(\theta^k)\Delta\theta = x_d - f(\theta^k)
$$

where $\Delta\theta = \theta^{k+1} - \theta^k$ and $J(\theta)$ is the Jacobian matrix of $f(\theta)$.

To get $\Delta\theta$, we need to consider the dimension of $J$. If $J$ is invertible, just pre-multiply $J^{-1}$ to both sides. Otherwise, we need to use the **pseudoinverse** of $J$, denoted as $J^{\dagger}$. The idea behind $J^{\dagger}$ is we want to minimize $\Delta\theta$.

### The Case of 

## Python Solution for Course Quiz

~~~python
import numpy as np
from numpy import linalg as LA
import modern_robotics as mr
import math

pi = math.pi

# question 1
def function_1(theta):
    return np.array([theta[0]**2-9, theta[1]**2-4])

def jacobian_1(theta):
    return np.array([[2*theta[0], 0], [0, 2*theta[1]]])

count = 0       # iteration counter
theta = [1, 1]  # initial guess
while(count < 2):
    count = count + 1
    theta = theta - np.dot(LA.inv(jacobian_1(theta)), function_1(theta))

theta = np.around(theta, decimals=2)
print("\nQuestion 1:\n", np.array2string(theta, separator=','))

# question 2
Blist = np.array([[ 0, 0, 1, 0, 3, 0],
                  [ 0, 0, 1, 0, 2, 0],
                  [ 0, 0, 1, 0, 1, 0]]).T
M = np.array([[ 1, 0, 0, 3],
              [ 0, 1, 0, 0],
              [ 0, 0, 1, 0],
              [ 0, 0, 0, 1]])
T = np.array([[-0.585, -0.811, 0, 0.076],
              [ 0.811, -0.585, 0, 2.608],
              [ 0    ,  0    , 1, 0.   ],
              [ 0    ,  0.   , 0, 1.   ]])
thetalist0 = np.array([pi/4, pi/4, pi/4])
eomg = 0.001
ev = 0.0001

(thetalistQ2, successQ2) = mr.IKinBody(Blist, M, T, thetalist0, eomg, ev)

if(successQ2):
    thetalistQ2 = np.around(thetalistQ2, decimals=2)
    print("\nQuestion 2:\n", np.array2string(thetalistQ2, separator=','))
else:
    print("\nQuestion 2:\n", "Function \'IKinBody\' failed to converge.")
~~~

## References

[1] [Newton's method (Wikipedia)](https://en.wikipedia.org/wiki/Newton%27s_method)
