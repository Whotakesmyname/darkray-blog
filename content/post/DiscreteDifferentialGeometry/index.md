---
title: "Discrete Differential Geometry"
description: Course Notes for CMU 15-858 Discrete Differential Geometry in Spring 2022
date: 2022-01-24T17:29:59-05:00
image: ricardo-gomez-angel-unsplash.jpg
tags: ['course note', geometry]
categories: ['Computer Graphics', Mathematics]
math: true
toc: true
---

Two or three months ago, if you mentioned DDG when talking to me,
I would say I will keep away from geometry as far as I can. Things changed 1 month ago. I wanted to gain a
deeper understanding of the part about logarithm rotation mapping in last semester's Computer Graphics course. Although I managed to get a high grade, but to be honest I cannot say I totally know all the details about it. I was really interested in how the logarithm mapping of rotation matrices can result in a smooth interpolation. I found a strong connection between skew-symmetric matrix and $\text{SO}(n)$. Following that, I found Lie Algebra. I headed to a course on Lie Group & Lie Algebra, and the instructor said "a beautiful way to understand Lie Group is that it is a special manifold.". So I came to find something about Differential Geometry, then I found this course I swore I would not take.

Another reason I take this course because it is taught by Professor Crane. In the last semester, he taught Computer Graphics in an impressively clear way. I could understand every word he said unless I fell asleep (Do not take 48 units of CS courses in CMU). I believe he can reduce much of the pain geometry brings to me.

<!-- more -->

## L1 Overview

This course (and actually Discrete Differential Geometry itself) will put considerable importance on computational processing and practical coding.

Think shape in two ways:
- mathematically (differential geometry)
- computationally (geometry processing)

Differential Geometry talks about **local properties of shape** and their relationships with **global properties of shape**.

### No Free Lunch
No single discrete definition captures **all** properties of its smooth counterpart.

### Case Study: Curvature
We think curve as parameterized.  
*This seems to be a common sense in differential geometry, because I saw this once in another post about differential geometry where it said taking 3D manifolds as a parameterized mapping from $\mathbb{R}^2 \rightarrow \mathbb{R}^3$.*

#### Tangent of a Curve
A vector is *tangent* to a curve if it "just barely grazes" the curve.

Formally, the **unit tangent** of a parameterized curve is the map obtained by normalizing its first derivative:

{{<asis>}}
$$
T(s) \coloneqq \frac{d}{ds} \gamma(s) \bigg/ \left| \frac{d}{ds} \gamma(s) \right|
$$
{{</asis>}}

*Assume curve never slows to a stop, making the derivative's norm become 0.*

If the derivative is already unit length, then we say it is **arc-length parameterized** and can omit the normalization denominator.

#### P.S.: What do we mean by parameterizing a curve?
*Later we will talk about "speed" of travel along a curve which makes me confused. So here I would like to stop and thinking what we are actually doing when parameterizing a curve.*

In this chapter's simple examples, we are working on some $\mathbb{R}^2$ curves, which are mapped from some $s \in \mathbb{R}$ in a closed range. By saying "speed", we can take the $s$ as a parameter indicating time. So the mapping represents a time-position function, and we can have this function's first derivative as this trace's speed naturally. The direction is the speed direction
