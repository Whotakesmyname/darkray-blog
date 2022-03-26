---
title: "Advanced Physics Engines"
description: "Notes of GAMES201 - Advanced Physics Engines"
date: 2022-03-26T00:27:38-04:00
image: michael-dziedzic.jpg
tags: ['course note', 'simulation']
categories: ['Computer Graphics']
math: true
toc: true
---

This course is about how to write a Physics Engine / do some physics simulation, given by [Yuanming Hu](https://yuanming.taichi.graphics/). I think one of the purposes that why Yuanming gave this course is to preach about his amazing work [Taichi](https://taichi.graphics/), a handy tool for parallel programming, especially for Computer Graphics. I definitely recommend taking a look at that TensorFlow-like powerful cross-platform tool with built-in JIT optimization.

However, my main purpose to take this course is to prepare for implementing a GPU MPM solver, so those language-related parts are not recorded in this note.


## L2 Lagrangian View 1
Lagrangian view cares about particles in the continuums. Particles move with the flow. Eulerian view puts still sensors in the flow, measuring properties of the field.

### Continuum Simulation
Usually Lagrangian representation uses particles, triangles, or other primitives to represent small units of continuums. Eulerian one uses grids often.

### Mass-Spring Systems
Which is quite similar to us. It is mentioned in the basic Computer Graphics animation chapter. Refer to the Computer Graphics notes.

#### Implementation Trick
After working out velocity, immediately check boundary constraints (e.g. ground collision). If the constraints are broken, fix it and set velocity to 0. After all those checks, update position based on velocity.

### Explicit v.s. Implicit Time Integrators
Explicit (forward Euler, symplectic Euler, RK, ...)
- Future depends only on past
- Easy to implement
- Easy to explode $\Delta t \le c \sqrt{m/k}$ (c ~ 1)
- Bad for stiff materials

Why explode? The time step cannot capture more than 1 cycle spring oscillation.

Implicit (backward Euler, middle-point, ...)
- Future depends on both future and past
- Chicken-egg problem: need to solve a linear system
- Harder to implement
- Allow larger time step generally
- Numerical damping and locking

#### Convert implicit methods to a linear system
Linearize the non-linear $f(x_t)$ by expanding to its first-order Taylor series.
{{<asis>}}
$$
\begin{align*}
v_{t+1} &= v_t + \Delta t M^{-1}f(x_{t+1}) \\
v_{t+1} &= v_t + \Delta t M^{-1}f(x_t) + \Delta t^2 M^{-1}\frac{\partial f}{\partial x} (x_t)v_{t+1} \\
(I - \Delta t^2 M^{-1}\frac{\partial f}{\partial x}(x_t))v_{t+1} &= v_t + \Delta t M^{-1}f(x_t)
\end{align*}
$$
{{</asis>}}

##### Unifying Explicit and Implicit Method
By adding a hyperparameter $\beta$ to the above formula, we can unify those methods:

{{<asis>}}
$$
(I - \beta \Delta t^2 M^{-1}\frac{\partial f}{\partial x}(x_t))v_{t+1} = v_t + \Delta t M^{-1}f(x_t)
$$
{{</asis>}}

- $\beta = 0$: Explicit methods
- $\beta = 0.5$: Middle point method
- $\beta = 1$: Implicit method

### How to solve linear system?
With implicit method, we often need to solve a linear system to get the answer.

To solve a linear system, generally we have following ways:
- Jacobi / Gauss-Seidel iteration
- Conjugate gradient
- Preconditioning

> Extending reading:
> - Fast Simulation of Mass-Spring System
