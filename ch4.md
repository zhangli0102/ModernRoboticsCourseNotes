---
layout: post
mathjax: true
title: 'Chapter 4: Forward Kinematics'
description: Modern Robotics Course Notes
is_project_page: false
---

<p style="text-align:center;">
<button type="button" onclick="window.location.href='index.html';">Homepage</button>
<span style="float:left;"><button type="button" onclick="window.location.href='ch3.html';">Previous</button></span>
<span style="float:right;"><button type="button" onclick="window.location.href='ch5.html';">Next</button></span>
</p>

## Introduction

***Forward kinematics** of a robot refers to the calculation of the position and orientation of its end-effector frame from its joint coordinates $\theta$.*

This chapter focuses on open chain structures. There two widly used representations for the forward kinematics of general open chains: **Denavit-Hartenberg(D-H) parameters** and **product of exponentials(PoE) formula**. The latter one is our preferred choice of forward kinematics representation and the topic of this chapter.

***

## Product of Exponentials(PoE) Formula

The general idea of PoE formula is using screw motion(matrix exponential) to describe the displacement at each joint of the robot. 

The general step of using PoE formula for forward kinematics can be concluded as:
1. Find the zero/home position configuration $M \in SE(3)$ of the robot, where all joints are set to zero and the robot is at initial position.;
2. Calculate the set of screw-axes of all the joints $$\mathcal{S}= \{ \mathcal{S}_1, \mathcal{S}_2, \cdots , \mathcal{S}_n \}$$;
3. Get the set of all joint variables $\theta$;
4. Use PoE formula to calculate the final configuration $T(\theta) \in SE(3)$.

Usually, there is a base frame $$\{s\}$$ and a body frame $$\{b\}$$, which is assigned with the end-effector. Representing screw-axes in different frames results two forms of PoE formula: **space form** and **body form**.

### Space Form of PoE Formula

In the space-form of PoE formula, $M$ and screw-axes are represented in the fixed/space/base frame. Here recall that, when calculating screw-axes, the robot must be at zero/home position.
After finishing the first three steps mentioned above, the space form of PoE formula can be written as:

$$T(\theta) = e^{[\mathcal{S}_1]\theta_1} \cdots e^{[\mathcal{S}_{n-1}]\theta_{n-1}} \underbrace{e^{[\mathcal{S}_n]\theta_n} M}_{\text{start from the distal}}$$

Here, *$M$ is first transformed by the most distal joint, progressively moving inward to more proximal joints. Note that the fixed space-frame representation of the scrwe axis for a more proximal joint is not affected by the joint displacement at a distal joint.*

### Body Form of PoE Formula

In the body-form of PoE formula, $M$ is still represented in the space frame. However, all the screw-axes are represented in the body frame. In this case, the PoE formula is written as:

$$
T(\theta) = \underbrace{M e^{[\mathcal{B}_1]\theta_1}}_{\text{start from the proximal}} \cdots e^{[\mathcal{B}_{n-1}]\theta_{n-1}} e^{[\mathcal{B}_n]\theta_n}
$$

Here, *$M$ is first transformed by the first joint, progressively moving outward to more distal joints. The body-frame representation of the screw axis for a more distal joint is not affected by the joint displacement at a proximal joint.*

The order of transformations plays an important role here, wrong orders can lead to wrong configurations(you can try it). But once the PoE formula is represented correctly, the order of operations on the joints has no effect on the end-effector's final configuration.

***

## Python Solution for Coursera Quiz

```python
import numpy as np 
import modern_robotics as mr 
import math

pi = math.pi

# question 1
M = np.array([[    1,    0,    0, 3.73],
              [    0,    1,    0,    0],
              [    0,    0,    1, 2.73],
              [    0,    0,    0,    1]])
print("\nQuestion 1:\n", np.array2string(M, separator=',', suppress_small=True))

# question 2
Slist = np.array([[    0,    0,    1,    0,   -1,    0],
                  [    0,    1,    0,    0,    0,    1],
                  [    0,    1,    0,    1,    0, 2.73],
                  [    0,    1,    0,-0.73,    0, 3.73],
                  [    0,    0,    0,    0,    0,    1],
                  [    0,    0,    1,    0,    -3.73,0]]).T
print("\nQuestion 2:\n", np.array2string(Slist, separator=',', suppress_small=True))

# question 3
Blist = np.array([[    0,    0,    1,    0, 2.73,    0],
                  [    0,    1,    0, 2.73,    0,-2.73],
                  [    0,    1,    0, 3.73,    0,   -1],
                  [    0,    1,    0,    2,    0,    0],
                  [    0,    0,    0,    0,    0,    1],
                  [    0,    0,    1,    0,    0,    0]]).T
print("\nQuestion 3:\n", np.array2string(Blist, separator=',', suppress_small=True))

# question 4
thetalist_space = np.array([-pi/2, pi/2, pi/3, -pi/4, 1, pi/6])
MatSpace = mr.FKinSpace(M, Slist, thetalist_space) 
MatSpaceOff = np.around(MatSpace, decimals=2)
print("\nQuestion 4:\n", np.array2string(MatSpaceOff, separator=',', suppress_small=True))

# question 5
thetalist_body = np.array([-pi/2, pi/2, pi/3, -pi/4, 1, pi/6])
MatBody = mr.FKinBody(M, Blist, thetalist_body)
MatBodyOff = np.around(MatBody, decimals=2)
print("\nQuestion 5:\n", np.array2string(MatBodyOff, separator=',', suppress_small=True))
```