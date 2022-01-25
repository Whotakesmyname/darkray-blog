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

#### Personal Thinking: What do we mean by parameterizing a curve?
*Later we will talk about "speed" of travel along a curve which makes me confused. So here I would like to stop and think what we are actually doing when parameterizing a curve.*

In this chapter's simple examples, we are working on some $\mathbb{R}^2$ curves, which are mapped from some $s \in \mathbb{R}$ in a closed range. By saying "speed", we can take the $s$ as a parameter indicating time. So the mapping represents a time-position function, and we can have this function's first derivative as this trace's speed naturally. The direction is the velocity direction, and the norm is the scalar velocity.

Later we will see another concept, **arc-length parameterization**. It is a special class of parameterization of curves. As mentioned above, we can take the parameter $s$ as time $t$ and see the curve as a trace we travel along in a time interval. However, there are many ways how we travel along the trace. We can have different speed at different time, although the velocity direction at each point on curve should be the same. **Arc-length parameterization** gives us a curve that we travel along it in a uniform speed, which means the norm of its first derivative is constant, and more specifically, always 1. In this way, our parameter is in fact this curve's length. This is also how we name it.

Another thing worth mentioning is that the parametric curve $\gamma$ and its image $\gamma(I)$ must be distinguished because a given subset of $\mathbb{R}^n$ can be the image of several distinct parametric curves. Only the mapping function $\gamma$ itself is a curve, instead of the image which is its mapping result.

#### Normal of a Curve
A **unit normal** is expressed as a quarter-rotation $\mathcal{J}$ of the unit tangent in the counter-clockwise direction:

{{<asis>}}
$$
N(s) \coloneqq \mathcal{J} T(s)
$$
{{</asis>}}

#### Curvature of a Plane Curve
Curvature describes "how much a curve bends". More formally, the curvature of an *arc-length parameterized* plane curve can be expressed as the rate of change in the tangent.

Curvature has sign. Thus, we use the result of first derivative tangent dot normal.

{{<asis>}}
$$
\mathcal{k}(s) \coloneqq \langle N(s), \frac{d}{ds}T(s) \rangle = \langle N(s) , \frac{d^2}{ds^2} \gamma(s) \rangle
$$
{{</asis>}}

This definition cannot fit discrete scenario. We have to come up with something else.

##### When is a Discrete Definition "Good"
Many criteria:
- satisfies (some of the) same properties/theorems as smooth curvature
- converges to smooth value as we refine our curve
- efficient to compute / solve equations

##### Several Other Curvature Definitions
There are some other definitions of curvature who are equivalent in smooth setting
- Turning angle
- Length variation
- Steiner formula
- Osculating circle

They will have different consequence in discrete setting.

###### Turning Angle

## L3 Mesh

### Orientation
#### Simplex Orientation
We can represent a simplex by listing their vertices as an ordered tuple.
An oriented k-simplex is an ordered tuple, up to even permutation.

We may find some connections between matrix determinant and orientation by thinking a k-simplex as k-1 vectors.

#### Oriented Simplicial Complex
**Definition.** An oriented simplicial complex is a simplicial complex where each simplex is given an ordering.

#### Relative Orientation
**Definition.** Two distinct oriented simplices have the same **relative orientation** if the two (maximal) faces in their intersection have **opposite** orientation.

*This is actually an observation: if two connected triangles have same orientation (so they have same **relative orientation**), on the connected edge, they have opposite orientation.*

### Simplicial Manifold
#### What is Manifold?
It's kind of "nice" space in geometry. Manifold "looks like" $\mathbb{R}^n$ locally.

**Definition.** A simplicial $k$-complex is manifold if the **link** of every vertex looks like a $(k-1)$-dimensional sphere.

By saying **link**, we mean a closure of "star".

#### Manifold Triangle Mesh
- every edge is contained in 2 triangles or just one along the boundary
- every vertex is contained in a single loop of triangles or a single fan along the boundary

#### Why Manifold Meshes
- Could allow arbitrary meshes
- ...

#### Topological Data Structures
##### Adjacency List
Listing triangles
- Store only top-dimensional simplices
- Pros: simple, small storage cost
- Cons: hard to iterate over e.g. edges; expensive to access neighbors

##### Incidence Matrix

$E^0$ represents relations between vertices

$E^1$ represents relations between edges and triangles

... TODO: mathematical definition

##### Sparse Matrix Data Structures
An interesting format: **Compressed column format**

```
values = {values of the matrix};
row indices = {same length as values indicating row idx};
cumulative entries # by column = {cumulative sum of number of values per col};
```

##### Half Edge Mesh
You should have learnt that in Computer Graphics.

##### Quad Edges
Skipped