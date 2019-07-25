---
layout: post
mathjax: true
title: 'Chapter 2: Configuration Space'
description: Modern Robotics Course Notes
is_project_page: false
---


<p style="text-align:center;">
<button type="button" onclick="window.location.href='index.html';">Homepage</button>
<span style="float:left;"><button type="button" onclick="alert('This is the first chapter!')">Previous</button></span>
<span style="float:right;"><button type="button" onclick="window.location.href='ch3.html';">Next</button></span>
</p>

## Some Terms
**Joints**, **links**, and **end-effecto**r.

***

## Some Definitions
*"The **configuration** of a robot is a complete specification of the position of every point of the robot. The minimum number $$n$$ of real-valued coordinates needed to represent the configuration is the number of **degrees of freedom (dof)** of the robot. The $$n$$-dimensional space containing all possible configuration of the robot is called the **configuration space (C-space)**. The configuration space of a robot is represented by a point the C-space."*

***

## Degrees of Freedom of a Rigid Body

How to determine the dof of a coin on plane?

* Two parameters to determine point $A$
* One parameter to determine point $B$ (why?)
* Point $C$ can determined from $A$ and $B$, so it requires no parameter.

How about a coin in 3-dimensional space?

For a rigid body in $n$-dimensional space, its dof is $m$, then we have:

$$
m = \sum_{i=0}^{n} i = \frac{1}{2} n (n-1)
$$

in which $n$ degrees are *linear* and all the rest degrees are *angular*.

***

## Constraints of Joints

Joints will constrain the dof of a rigid body.

**What are the constrains of several important joints(especially revolute, prismatic, and spherical) in both planar and spatial space?**

***

## Grübler's Formula

This formula is to calculate the *the number of degrees of freedom of a mechanism with links and joints*.

It's based on a general rule(which can be helpful in some cases!): `degrees of freedom = (sum of freedoms of the bodies) - (number of independent constraints)`. Remember, here the constraints must be **independent**.

Given a mechanism consisting of $N$ links(including the gound as a link), $J$ joints. The number of degrees of freedom of a body is $m$, the number of freedoms and constraints provided by joint $i$ are $f_i$ and $c_i$, Grübler's formula can written as:

$$
\begin{aligned}
dof = & \underbrace{m(N-1)}_{\text{rigid body freedoms}} - \underbrace{\sum_{i=1}^{J}c_i}_{\text{joint constraints}} \\
    = & m(J-1-N) + \sum_{i=1}^{J}f_i
\end{aligned}
$$

### Several Notes for Applyging Grübler's Formula {#notes_grubler}

1. The ground is always considered as a link, but its number of degrees of freedom is 0.
2. Careful with **overlapping joints**: example 2.5, page 20 of the textbook.
3. Careful with **redundant constraints and sigularities**: example 2.6, page 21 of the textbook. Recall that Grübler's formula holds only if the constraints are independent, if they are NOT independent, then the result of Grübler's formula serves as the lower bound on the dof(**In this case, the dof can be negative**). Configuration space sigularities arising in closed chains are discussed later.
4. The Delta robot: example 2.7, page 22 of the textbook. Even though the dof calculated from Grübler's formula is 15, but only 3 are visible at the end-effector. This is because that one degree of freedom in a spherical joint is for torsion.

### Some Quiz Questions

1. (Question 3 of quiz of chapter 2, part 1) Assume your arm, from your shoulder to your palm, has 7 degrees of freedom. You are carrying a tray like a waiter, and you must keep the tray horizontal to avoid spilling drinks on the tray. How many degrees of freedom does your arm have while satisfying the constraint that the tray stays horizontal? Your answer should be an integer.
   * The answer is 5.
   * *The requirement that the tray be horizontal places two constraints on its orientation: the rotation of the tray about two axes defining the horizontal plane of the tray must be zero. (In other words, the roll and the pitch of the tray are zero.)*
2. (Question 4&5 of quiz of chapter 2, part 1) A total of $n$ identical SRS arms are grasping a common object as shown below. Find the number of degrees of freedom of this system while the grippers hold the object rigidly (no relative motion between the object and the last links of the SRS arms). Your answer should be a mathematical expression including $n$.
   * The answer is $n+6$.
   * Note that all the graspers, together with the object, should be considered as a single link. 
![q1_4](https://raw.githubusercontent.com/MuchenSun/ModernRoboticsCourseNotes/master/_img/ch2_f1.jpg)
3. (Question 7 of quiz of chapter 2, part 1) Use the planar version of Grubler's formula to determine the number of degrees of freedom of the mechanism shown below. Your answer should be an integer.
   * The answer is 3.
   * Removing all the **redundant joints**, this mechanism is same with an open-chain, 3R arm. 
![q1_7](https://raw.githubusercontent.com/MuchenSun/ModernRoboticsCourseNotes/master/_img/ch2_f2.jpg)

***

## Configuration Space: Topology and Representation

### Configuration Space Topology

In what case, two configuration spaces are **topologically equivalent**?

What's the difference between $T^2 = S^1 \times S^1$ and $S^2$?

**What's the total C-space of a rigid body in 3-dimensional space? (Recall the process of determining the degrees of freedom of a rigid body in space by choosing three points on the body)**

### Configuration Space Representation

**Explicit Representation**:
* Uses $n$ parameters to represent a $n$-dimensional space.
* But it has sigularities, for example the North/South pole situation.
* There are two solutions for the sigularities:
  * Use multiple coordinate charts (atlas).
  * Use **implicit representation**.

**Implicit Representation**:
* Uses more parameters than the space's dof.
* Has advantage in representing closed-chain mechanism

***

## Configuration and Velocity Constraints

A planar, closed-chain, four-bar linkage has a dof of one, but representing this dof can be hard by using **explicit representation**. With **loop-closure equations**, however, **implicit representation** is more useful in this case. 

**Holonomic Constraints**:
* Constraints that reduce the dimension of C-space.
* The C-space can be viewed as a surface of dimension $n-k$ embedded in $\mathbb{R}^n$, where $n$ indicates the number of parameters that are used to represent the space, and $k$ indicates the number of constraints. (In the example of the planar, closed-chain, four-bar linkage, $n=4$ and $k=3$)

**Pfaffian Constraints**
* Velocity constraints in the form of $A(\theta)\dot{\theta} = 0$ are named Pfaffian constraints. (Where does this form come from?)
* Some of them are the derivative of a set of holonomic constraints.

**Nonholonomic Constraints**
* Pfaffian constraints that are nonintegrable. (Figure 2.11: A coin rolling on a plane without slipping)
* Reduces the dimension of feasible velocities of the system, but do not reduce the reachable C-space. (The rolling coin can reach any place in its four-dimensional C-space despite the two constraints on its velocity)

***

## Task Space and Work Space

**Task Space**: The space in which the robot's task is naturally described.

**Work Space**: A specification of the reachable configurations of the end-effector.

Both spaces are distinct from the robot's C-space.

***

## My Questions

### Configuration Space Singularity in Five-Bar Linkage

In Figure 2.7(b), as shown below, if the two joints connected to the ground are locked, then the mechanism has a dof of zero. This is true in most cases, and can be verified by Grübler's formula. (freedoms of the locked joints and other joints are zero and one)

![ch2_f7](https://raw.githubusercontent.com/MuchenSun/ModernRoboticsCourseNotes/master/_img/ch2_f7.jpg)

However, this mechanism has a singularity, as shown in the right sub-figure. If the two middle links have a same length and overlap each other, then these two links can rotate together freely.

**So, is Grübler's formula applicable in this case?**

My solution is, for this singularity, similar with the mechanism in figure (a), the two links of one side (right or left), together with the three joints, have no effect on the motion of the mechanism. Thus, recall that $m=3, N=3, J=2$, and $f_1=1, f_2=0$, $dof = m(N-1-J) + f_1 + f_2 = 1$.

### The Human Arm Problem

For the human arm problem mentioned above, the given solution indicates that there are two constraints on the palm to keep the tray horizontal. However, if we assume the arm is a SRS mechanism, which is the assumption of the given solution, the dof should be **seven**. Even though the tray must be horizontal, the spherical joint can still move in all three degrees, with the corporation with the rest two joints. Is my understanding correct?
