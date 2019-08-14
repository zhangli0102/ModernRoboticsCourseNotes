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

The **inverse kinematics** is the opposite problem of **forward kinematics**(not the velocity kinematics problem discussed in the last chapter), it aims to calculate a set of joint values given a homogeneous transformation matrix representing the transformation between current configuration and desired configuration of the end-effector. 

These are two common solutions for inverse kinematics problem. Analytic solution depends on the structure of the robot. They can be easily obtained from some popluar robot structures like PUMA and Stanford robot arms (both are discussed in the book). However, numerical method is a better idea in more general cases. Here, the numerical method is adapted from Newton-Raphson method.

***

## Newton-Raphson Iterative Algorithm

> ... **Newton's method**, also known as **Newton-Raphson method**, ..., is a root-finding algorithm which produces successively better approximations to the roots (or zeros) of a real-valued function. <sup>[1]</sup>

For an iteration method, as well as recursion, I think the key issue is to find out the relation between two iterations. In the case of Newton-Raphson method, this relation is obtained by applying Taylor expansion to the target function and truncate it at first order.

***

## Numerical Inverse Kinematics Problem

### The Case of Minimal Set of Coordinates

If we set the forward kinematics $f(\theta)$ as target funtion, in the inverse kinematics problem, instead of finding its root, we want to find the $\theta_d$ that makes $f(\theta_d) = x_d$, which is not a root-finding problem. To apply Newton-Raphson method, we design a new function $g(\theta) = x_d - f(\theta)$ to make it a root-finding problem.

Similarly, we can get the relation between two iterations by applying Taylor expansion. Then, we can get the equation which I think is the most important one to understand numerical inverse kinematics problem:

$$
J(\theta^k)\Delta\theta = x_d - f(\theta^k)
$$

where $\Delta\theta = \theta^{k+1} - \theta^k$ and $J(\theta)$ is the Jacobian matrix of $f(\theta)$.

To get $\Delta\theta$, we need to consider the dimension of $J$. If $J$ is invertible, just pre-multiply $J^{-1}$ to both sides. Otherwise, we need to use the **pseudoinverse** of $J$, denoted as $J^{\dagger}$. The idea behind $J^{\dagger}$ is we want to minimize $\Delta\theta$.

### The Case of $SE(3)$

For now we have:

$$
\Delta\theta = J^{\dagger} (x_d - f(\theta^k))
$$

where $e = x_d - f(\theta^k)$ can be considered as *a velocity vector which, if followed for unit time, would cause a motion from $f(\theta^k)$ to $x_d$*. When representing end-effector configuration with a transformation matrix $T \in SE(3)$, we also need a velocity value that described the same motion, that's why we calculate the matrix logarithm of $T_{bd}(\theta^i)$.

***

*(Since the rest two sections of this chpater in the book: "Inverse Velocity Kinematics" and "A Note on Closed Loops" are not mentioned in detail in the online course, I will skip these two section temporarily.)*

***

## Connections Between Chapter 3 and 6

In this chapter, we use a twist $\mathcal{V}_b$ to describe a velocity which, *if followed for unit time*, would cause a motion from $$T_{sb}$$ to $$T_{sd}$$. We obtain this twist by:

$$
[\mathcal{V}_b] = log(T_{bd}(\theta^i))
$$

Similar idea has already mentioned in the textbook. At *section 3.2.3.3 "Matrix Logarithm of Rotations"* of the textbook, there is:

> Just as the matrix exponential "integrates" the matrix representation of an angular velocity $[\hat{\omega}]\theta \in so(3)$ for one second to give an orientation $R \in SO(3)$, the matrix logarithm "differentiates" an $R \in SO(3)$ to find the matrix representation of a constant angular velocity $[\hat{\omega}]\theta \in so(3)$ which, if integrated for one second, rotates a frame from $I$ to $R$.

This idea came from *section 3.2.3.2 "Exponential Coordinates of Rotations"* of the textbook, when the authors considered a rotation transformation along an axis $\hat{\omega}$ for $\theta$ radians as achieved by rotating in a constant speed of 1 rad/s for $\theta$ seconds (or in a constant speed of $\theta$ rad/s for one second).

It is quite straightforward to employ this idea to the case of twist and homogeneous transformtion matrix. In *proposition 3.25* of the textbook, we can even determine that directly by seeing components of $e^{[\mathcal{S}]\theta}$.

***

## Singularity, Redundant, Fat and Tall

Here "singularity", "redundant", "fat", and "tall" might be confusing sometimes, let's figure them out.

First, a mechanism is at **singularity**, when its Jacobian matrix fails to be of maximal rank, which means at least two columns or two rows of the the matrix are aligned. In this situation, the robot loses the ability to move instantaneously in one or more directions.

Second, a mechanism is **redundant**, when its Jacobian matrix has more than 6 columns. In this situation, the robot has more DoF than the task space, which will generate internal motions that are not evident in the motion of the end-effector.

Third, a Jacobian matrix is **tall** when it has less columns than rows, while the matrix is "fat" when it has more columns than rows.

***

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

***

## References

[1] [Newton's method (Wikipedia)](https://en.wikipedia.org/wiki/Newton%27s_method)
