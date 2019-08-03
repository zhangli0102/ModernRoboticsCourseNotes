---
layout: post
mathjax: true
title: 'Chapter 8: Dynamics of Open Chains'
description: Modern Robotics Course Notes
is_project_page: false
---

<p style="text-align:center;">
<button type="button" onclick="window.location.href='index.html';">Homepage</button>
<span style="float:left;"><button type="button" onclick="window.location.href='ch6.html';">Previous</button></span>
<span style="float:right;"><button type="button" onclick="alert('This is the last chapter!')">Next</button></span>
</p>


## Introduction

The topic of this chapter is **robot dynamics**, which discusses the relation between joint accelerations $\ddot{\theta}$ and joint forces and torques $\tau$ with given joint positions $\theta$ and joint velocities $\dot{\theta}$. This relation is usually described by a set of equations named **dynamic equations / equations of motion**:

$$
\begin{align}
\tau &= M(\theta)\ddot{\theta} + h(\theta, \dot{\theta}) \tag*{(for inverse dynamics)}
\end{align}
$$

$$
\begin{align}
\ddot{\theta} &= M^{-1}(\theta) (\tau - h(\theta, \dot{\theta}))  \tag*{(for forward dynamics)}
\end{align}
$$

Typically, there are two ways to derive these equations, one is **Newton-Euler formulation**, another is **Lagrangian dynamics formulation**. We will start with the latter one.

## Lagrangian Formulation


