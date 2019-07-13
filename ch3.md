---
layout: default
mathjax: true
title: 'Chapter 3: Rigid-Body Motions'
description: Modern Robotics Course Notes
---

<p style="text-align:center;">
<button onclick="window.location.href='index.html';">Homepage</button>
<span style="float:left;"><button onclick="window.location.href='ch2.html';">Previous</button></span>
<span style="float:right;"><button onclick="window.location.href='ch3.html';">Next</button></span>
</p>

## Rotations and Angular Velocities

### Rotation Matrices

A rotation matrix $$R$$ is in $$SO(3)$$(*special orthogonal group*). What's the definition of $$SO(3)$$? What does this definition mean in geometry?

Rotation matrices have several properties:
1. closure
2. associativity
3. identity element existence
4. inverse element existence 

#### Usages of Rotation Matrices
**Representing an orientation**

Here the rotation matrix $$R_{sb}$$ can be considered as the representation of the three axes in $$\{b\}$$ at $$\{s\}$$. For any two frame $$\{d\}$$ and $$\{e\}$$, we have

$$
R_{ed} = R^T_{de} = R^{-1}_{de}
$$

**Changing the reference frame**

Here the rotation matrix $$R_{sb}$$ can be considered as an operation to change the reference frame of a matrix from $$\{s\}$$ to $$\{b\}$$. A subscription cancellation rule can be exploited.

**Rotating a vector or a frame**

Here the rotation matrix $$R_{sb}$$ can be considered as an operation that rotates $$\{s\}$$ about an axis $$\hat{\omega}$$ for $$\theta$$ to $$\{b\}$$. We have $$R = Rot(\hat{\omega}, \theta)$$. 

What's the matrix representation of $$R$$ with a given $$\hat{\omega} = (\omega_1, \omega_2, \omega_3)$$ and a $$\theta$$?

When using rotation matrix as rotation operation, $$\hat{\omega}$$ is coordinate-dependent, which means it indicates different vectors in difference reference frames. That's why pre-multiplying and post-multiplying a rotation matrix can have difference results. What's the difference between $$RR_{sb}$$ and $$R_{sb}R$$, where $$R_{sb}$$ is the rotation matrix?

### Angular Velocities
Angular velocity is defined as $$w = \hat{w}\dot{\theta}$$. It's a 3-vector, where $$\hat{w}$$ indicates the axis of rotation and $$\dot{\theta}$$ indicates the rate of rotation.

For a given body frame, whose orientation is represented by a rotation matrix $$R = \left[\begin{matrix}r_1&r_2&r_3\end{matrix}\right]$$. As it rotate with the fixed angular velocity $$\omega_s$$, which is described in $$\{s\}$$, $$R$$ is a function of time $$t$$. The rate of change of $$R$$ can then be represented as:

$$
\begin{aligned}
    \dot{R} & = \left[\begin{matrix} \omega_s \times r_1 & \omega_s \times r_1 & \omega_s \times r_1 \end{matrix}\right] \\
            & = \omega_s \times R
\end{aligned}
$$

Here, several things need to be explained:
1. The angular velocity $$w$$ is coordinate-free. A rigid-body is rotating around an origin point with a fixed rate. The angular velocity(according to Wikipedia[1], it should be an orbital angular velocity) is a 3-vector whose direction is prependicular to the rotation plane and magnitude is the rate of rotation. In the last example, $$\omega_s$$ is just its representation if the fixed frame.
2. How to understand $$\dot{R}$$(this question really confused me a lot)? Here I have two explanations:s
   1. As indicated above, for a rotating frame, its orientation is represented by the rotation matrix $$R$$, which can be viewed as a function about time $$t$$. So, as $$t$$ increases, R, as a $$3\times 3$$ matrix, all the nine elements will change. $$\dot{R}$$, then, indicates the rate of change of (all the elements of) $$R$$.
   2. $$R$$ is consisted by three unit vectors indicating three axes of the body frame. For a rotating point in space that is represented by a vector $$r$$, $$\omega \times r$$ indicates the linear velocity of the point. Thus, $$\dot{R}$$ can also be viewed as the linear velocities of the three vertices of the body frame.

Now we have $$\dot{R} = \omega_s \times R$$, but cross production is not convenient, so we have the **skew-symmetric** matrix representation of a angular velocity $$\omega_s \in \mathbb{R}^3$$, which is written as $$[x] \in so(3)$$. What's the common property of a matrix in $$so(3)$$?

Skew-symmetric matrix representation has several important and useful properites:

$$R[\omega]R^T = [R\omega]$$

$$\dot{R}R^{-1} = [\omega_s]$$

$$R^{-1}\dot{R} = [\omega_b]$$

$$\omega_c = R_{cd}\omega_d$$

### Exponential Coordinates Representation of Rotation
Now, we know how to use 3 parameters to represent angular velocity. The next step is to use three parameters to represent rotation, which we named as **exponential coordinates representation of rotation**. Here we are going to do this by intergrating angular velocity.

First, a rotation can be described by an axis $\hat{\omega}$ and an angle $\theta$, writing them individually is the **axis-angle** representation of the rotation. Then, we have a vector $$p$$ doing such a rotation. Suppose the this rotation is achieved by rotating at a constant rate of rotation 1 rad/s for a time period of $t$. So, $t=\theta$. During the rotation, we have:

$$
\dot{p} = [\hat{\omega}] p
$$

this equation can be considered as a differential equation of the form $\dot{x}=Ax$, the solution is:

$$
p(t) = e^{[\hat{\omega}]t} p(0)
$$

where $p(0)$ is the initial position of vector $p$. Since the rate of rotation is 1 rad/s, we can replace $t$ by $\theta$, so we have:

$$
p(\theta) = e^{[\hat{\omega}]\theta} p(0)
$$

$e^{[\hat{\omega}]\theta}$ serves as a rotation matrix that rotates vector $p(0)$ to $p(\theta)$. $e^{[\hat{\omega}]\theta}$, after expansion, can be written as:

$$
e^{[\hat{\omega}]\theta} = I + \sin{\theta}[\hat{\omega}] + (1 - \cos{\theta})[\hat{\omega}]^2
$$

For a matrix $A \in \mathbb{R}^{n\times n}$, $e^A$ is the **matrix exponential** of $A$. Here the equation that uses the matrix exponential of $[\hat{\omega}]\theta$ to represent a rotation matrix is called **Rodrigues' formula**:

$$
Rot(\hat{\omega}, \theta) = e^{[\hat{\omega}]\theta} = I + \sin{\theta}[\hat{\omega}] + (1 - \cos{\theta})[\hat{\omega}]^2 \in SO(3)
$$

Recall that, when using rotation matrix as rotation operation, $$\hat{\omega}$$ is coordinate-dependent. Thus pre-multiplying and post-multiplying a rotation matrix can have difference results.

Since we can write the rotation matrix with given $\hat{\omega}$ and $\theta$, we also want to extract these two parameters from a given rotation matrix. Here the skew-symmetric matrix $[\hat{\omega}\theta] = [\hat{\omega}]\theta$ is named as the **matrix logarithm** of a rotation $R$. There is an algorithm to achieve this, the details and related provements of the algorithm can be found in the textbook at page 85 to 87.

### Summary of Rotations and Angular Velocities
We can use a **rotation matrix** in $SO(3)$ to represent the orientation of a body frame, which can also be exploited to rotate or change the reference frame of a frame or vector.

As rotation is a dynamic process, we want to know the rate of change of the body frame's orientation, so we introduce the concept of **angular velocity** to describe how a rigid-body(body frame) rotates at an instant.

Since we can use an unit vector and a constant(a rate of ratation) to represent angular velocity, we also want to use a unit vector and another constant(an angle) to represent rotation. To achieve this goal, we use **exponential coordinates representation of rotations**. Reserving the process, we have **matrix logarithm of a rotation**.

***

## Rigid-Body Motions and Twists

### Homogeneous Transformation Matrices
The idea of homogeneous transformation matrices is to describe both orientation and position of a body frame relative to the fixed frame. A homogeneous matrix $T$ can be written as:

$$
T = \left[\begin{matrix} R & p \\ 0 & 1 \end{matrix}\right] \in SE(3)
$$

where $R\in SO(3)$ is the rotation matrix, $p\in \mathbb{R}^3$ is a column vector, and $SE(3)$ is the **special Euclidean group / group of rigid-body motions / group of homogeneous transformation matrices**.

Homogeneous transformation matrices has several important properties. 

First, the inverse of the transformation matrix:

$$
T^{-1} = \left[\begin{matrix} R^T & -R^Tp \\ 0 & 1 \end{matrix}\right]$$

Second, since transformation matrix is $4\times 4$, we can no longer multiply it with a 3-vector $x$. Instead, we have the representation of $x$ in **homogeneuous coordinates** $\left[\begin{matrix}x^T \\ 1\end{matrix}\right]^T$. And we have:

$$
T \left[\begin{matrix} x \\ 1 \end{matrix}\right] = \left[\begin{matrix} Rx+p \\ 1 \end{matrix}\right]
$$

Third, according to the *proposition 3.18* at page 91 of the textbook, when serving as transformation operation, transformtion matrices preserves both length and angle of a shape, which is $SE(3)$ is the group of *rigid-body* motions.

#### Usages of Transformation Matrices
Analogous to rotation matrices, transformation matrices have three usages:

**Representing a configuration**

It's easy to understand, here we have:

$$
T_{ed} = T^{-1}_{de}
$$

**Changing the reference frame of a vector or a frame**

The subscript cancellation rule also applies here:

$$
T_{ab} T_{bc} = T_{ac}
$$

$$
T_{ab}v_b = v_a
$$

**Displacing(rotating and translating) a vector or a frame**

We have:

$$
T = (R, p) = (Rot(\hat{\omega}), p)
$$

so a transformation matrix can be seen as a combination of roation and translation operations.

Same with rotation matrix, transformation matrix here is coordinate-dependent. Whether we pre-multiply or post-multiply $T_{sb}$ by $T=(R,p)$ determines whether the $\hat{\omega}$-axis and $p$ are interpreted as in the fixed frame or the body frame:

*($\hat{\omega}$ is interpreted as in the fixed frame)*

$$
T_{sb'} = TT_{sb} = Trans(p) \underbrace{Rot(\hat{\omega}, \theta)T_{sb}}_{\text{rotate, then translate}}
$$

*($\hat{\omega}$ is interpreted as in the body frame)*

$$
T_{sb^{''}} = T_{sb}T = \underbrace{T_{sb} Trans(p)}_{\text{translate, then rotate}} Rot(\hat{\omega}, \theta)
$$

### Twists

Recall that rotation matrices have a property that:

$$\dot{R}R^{-1} = [\omega_s]$$

$$R^{-1}\dot{R} = [\omega_b]$$

Homogeneous transformation matrices have a similar one:

$$T^{-1} \dot{T} = \left[\begin{matrix} [\omega_b] & \nu_b \\ 0 & 0 \end{matrix}\right]$$

where $[\omega_b]$ is the angular velocity expressed in the $$\{b\}$$ frame. $\nu_b = R^T \dot{p}$, which can be written as $R_{bs}p_s = p_b$, is the linear velocity of the origin of frame $$\{b\}$$ expressed in $$\{b\}$$.

$$\dot{T} T^{-1} = \left[\begin{matrix} [\omega_s] & \nu_s \\ 0 & 0 \end{matrix}\right]$$

where $[\omega_s]$ is the angular velocity expressed in the $$\{s\}$$ frame. But, since $\dot{p}$ is the linear velocity of the body-frame origin expressed in the fixed frame, $$\nu_s = \dot{p} + \omega_s\times (-p)$$ means something else here: $-p$ is the vector pointing from the origin of $$\{b\}$$ to $$\{s\}$$, thus $\omega_s\times (-p)$ is the a linear velocity generated by rotation at the origin of $$\{s\}$$ expressed in $$\{s\}$$. As a result, $$\nu_s = \dot{p} + \omega_s\times (-p)$$ indicates the linear velocity of fixed frame origin expressed in fixed frame.

Now, we introduce the concept that simultaneously describes linear and angular velocity of a rigid-body, which is named **twist**:

$$\mathcal{V}_b = \left[\begin{matrix} \omega_b \\ \nu_b \end{matrix}\right] \in \mathbb{R}^6$$

is called **body twist / spatial velocity in the body frame**. The $4\times 4$ matrix representation of a body twist $\mathcal{V}_b$ is:

$$[\mathcal{V}_b] = T^{-1}\dot{T} \in se(3)$$

Similarly, we have:

$$\mathcal{V}_s = \left[\begin{matrix} \omega_s \\ \nu_s \end{matrix}\right] \in \mathbb{R}^6$$

is called **spatial twist / spatial velocity in the space frame**.The $4\times 4$ matrix representation of a spatial twist $\mathcal{V}_s$ is:

$$[\mathcal{V}_s] = \dot{T}T^{-1} \in se(3)$$

Spatial twist and body twist can be transferred to each other:

$$[\mathcal{V}_s] = T [\mathcal{V}_b] T^{-1}$$

$$\mathcal{V}_s = \left[\begin{matrix} R & 0 \\ [p]R & R \end{matrix}\right] \mathcal{V}_b$$

we extract the $6\times 6$ matrix, named it as **adjoint representation** $[Ad_T]$ of a transformation matrix $T$:

$$
[Ad_T] = \left[\begin{matrix} R & 0 \\ [p]R & R \end{matrix}\right] \in \mathbb{R}^{6\times 6}
$$

so we have:

$$
\mathcal{V}_s = [Ad_T] \mathcal{V}_b
$$

it can also be written as:

$$
\mathcal{V}_s = Ad_T(\mathcal{V}_b)
$$

Ajoint representation has several properties:

$$[Ad_T]^{-1} = [Ad_{T^{-1}}]$$

$$Ad_{T^{-1}}(Ad_T(\mathcal{V})) = Ad_{T^{-1}T}(\mathcal{V}) = Ad_I(\mathcal{V}) = \mathcal{V}$$

For any two frames $$\{c\}$$ and $$\{d\}$$, we have:

$$
\mathcal{V}_c = [Ad_{T_{cd}}] \mathcal{V}_d
$$

Again analogously to the case of angular velocities, it is important to realize that, for a given twist, its fixed-frame representation $\mathcal{V}_s$ does not depend on the choice of the body frame $$\{b\}$$, and its body-frame representation $\mathcal{V}_b$ does not depend on the choice of the fixed frame $$\{s\}$$.

#### The Skrew Interpretation of a Twist

The motion of a screw includes both linear and angular motions: it rotates to drill(translate) in one direction. Thus, similar with angular velocity, a twist can be interpreted in terms of s screw axis $\mathcal{S}$ and a velocity $\dot{\theta}$.

For a given reference frame, a **screw axis** $\mathcal{S}$ is written as:

$$\mathcal{S} = \left[\begin{matrix} \omega \\ \nu \end{matrix}\right] \in \mathbb{R}^6$$

even though looked like a twist, there only two conditions for $\omega$ and $\nu$:
1. $\lVert\omega\rVert = 1$. In this condition, $v = -\omega\times q + h\omega$, where $q$ is a point on the axis of the screw and $h$ is the pitch(=linear speed / angular speed) of the screw. If pitch is 0, the motion is pure rotation.
2. $\omega=0$. In this condition, $\lVert\nu\rVert = 1$, pitch is infinite and the motion is a pure translation along the screw axis defined by $\nu$.

A screw axis is a normalized twist, it has the $4\times 4$ matrix representations and its reference frame can be transferred by pre-multiplying adjoint representation of a transformtion matrix.

### Exponential Coordinates Representation of Rigid-Body Motions

The **Chasles-Mozzi theorem** states that every rigid-body displacement can be expressed as a displancement along a fixed screw axis $\mathcal{S}$ in space.

Based on the screw interpretation of a twist, we can define the six dimensional **exponential coordinates of a homogeneous transformation** $T$ as $\mathcal{S}\theta \in \mathbb{R}^6$, where $\mathcal{S}$ is the screw axis and $\theta$ is the distance that must be traveled along the screw axis to take frame from the origin $I$ to $T$.

By analogy to the rotation case, we have the **matrix exponential** of a transformation matrix $T$:

$$T = e^{[\mathcal{S}]\theta} = \left[\begin{matrix} e {[\omega]\theta} & G(\theta)\nu \\ 0 & 1 \end{matrix}\right]$$

$$G(\theta) = I\theta + (1 - \cos{\theta})[\omega] + (\theta - \sin{\theta})[\omega]^2$$

Again, "where there is a matrix exponential, there is a matrix logarithm". We have $[\mathcal{S}]\theta$ as the matrix logarithm of a given transforamtion matrix $T=(R,p)$. The details about this algorithm can be found at page 106 at the textbook.

### Summary of Rigid-Body Motions and Twists

We use **homogeneous transformation matrices** to describe rigid-body motions. By analogy to the case of rotation matrix, we have calculate the variable that describes the velocity of the moving rigid-body from transformation/rotation matrix, which is named **twist**. Different from angular velocity, changing the reference frame of a twist need the **adjoint representation** of a transformation matrix.

Twists can be interpreted in terms of a screw axis and a velocity. Based on this **screw interpretation** of a twist, we introduce the **exponential coordinates representation of a homogeneous transformation matrix**. Similar with the case of rotation matrix, we have **matrix exponential** and **matrix logarithm** of a transformation matrix.

***

## Wrenches

Given a reference frame $$\{a\}$$, a force $f_a \in \mathbb{R}^3$ creates a **torque / moment** $m_a \in \mathbb{R}^3$ after acting on a rigid body at point $r_a \in \mathbb{R}^3$:

$$m_a = r_a \times f_a$$

Combining moment and force together, we have the six-dimensional **spatial force / wrench** $$\mathcal{F}_a$$:

$$\mathcal{F}_a = \left[\begin{matrix} m_a \\ f_a \end{matrix}\right] \in \mathbb{R}^6 $$

Similar with the case of twist, the reference frame of a wrench can be switched with the help of the adjoint representation of a transformation matrix. But the case is slightly different here:

$$
\mathcal{F}_b = Ad^T_{T_{ab}}(\mathcal{F}_a) = [Ad_{T_{ab}}]^T\mathcal{F}_a
$$

***

## References

[1] [Angular velocity(Wikipedia)](https://en.wikipedia.org/wiki/Angular_velocity)

