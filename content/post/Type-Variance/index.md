---
title: "Covariance, Contravariance and Invariance"
description: Is a Cat[] an Animal[]?
date: 2022-01-26T21:55:09-05:00
image: rock-vincent-guitard-unsplash.jpg
tags: ['Type System']
categories: ['Computer Science']
math: true
---

This is something not so hard to understand, but also not so easy to figure out when you forget about it.
Why array of cat should not be a subtype of array of animal? Since I spent several minutes today on recalling the reason again, it might be
good for me to write down my reasoning now.

## Liskov Substitution Principle
To make things simple, we need a definition about what are we talking about when we say "A is a B". Here comes a handy rule which tells a natural
principle, which was introduced by Barbara Liskov in 1988. LSP mainly states that ***an object must be able to be replaced by its sub-object without breaking the program***.
It was formally and succinctly describled in a 1994 paper[^LSP-paper] as below:

> Subtype Requirement: Let $\phi(x)$ be a property provable about objects $x$ of type $\mathrm{T}$. Then $\phi(y)$ should be true of type $\mathrm{S}$ where $\mathrm{S}$ is a subtype of $\mathrm{T}$.

*Accurately, this is the definition of a particular subtyping relation,* **strong behavioral subtyping** *, and it is not perfect. But do not worry about those further details. It nicely works for our
superficial understanding of this topic.*

So, obviously, now we can be sure that *a cat is an animal*. A cat can do ***everything*** that an animal can do. What about an array of cats?

## Covariance
One natural choice is that, an array of cats ***is*** an array of animals. That works. When we ***take*** an element from this array of cats, it can do everything an animal can do.

```
Animal[] animals = Cat[] cats;
```

Since it is an array of animal, I surely can ***add*** a dog, into it, can I?

```
animals.add(new Dog());
```

No, I can't. Since the underlying array is an array of type cat, the `add()` function of the underlying array only expects inputs of type cat. It legally requires some special properties that only cats have, so providing other
animals does not work. The type system fails to prevent me to do this in this case. This is the case Java faces.

We tried to define the relation between these two composite types as the same relationship between their underlying types. This is called **Covariance**.

## Invariance
When we find covariance does not work in this case, we can compromise, saying that these two composite types have no relation between them. This is the safest way, and is the correct way for these mutable
container types. It's trivial. This is called **invariance**. The two types do not build relations based on their underlying types.

## Contravariance
Before ending this topic, let's see if we can find some more details. We can see covariance perfectly works when we ***take*** elements from the container, but failed when we want to ***write*** the container.
How can I make it right? I cannot write an animal to an array of cats. What about write a cat to an array of animals? Theoretically, it should work. An `add()` function expecting inputs of animals should accept
inputs of cats, because everything animals can do, every property animals have, cats can all do it and have it.

However, in real life, you can rarely do it. Obviously, a write-only container is hardly useful. The composite types have the inverse relationship of their underlying types, this is what we call **contravariance**.

## About Functions
For even more fun, we can think about the relationships between functions having related input and output types. If you take the example mentioned above as a case of two functions, you can figure it out easily.
When reading, we actually call `get()` which do not necessarily require any argument (it can always return the last element). The underlying type is its return value type. It is **covariant**.
When writing, we call `add(element)` which do not necessarily return any value. The argument type is, as we explained above, **contravariant**.

So we can safely say a function type A is a subtype of function B when A's arguments are supertypes of B's, and A's return type is subtype of B's.

[^LSP-paper]: Barbara H. Liskov and Jeannette M. Wing. 1994. A behavioral notion of subtyping. ACM Trans. Program. Lang. Syst. 16, 6 (Nov. 1994), 1811–1841. DOI:https://doi.org/10.1145/197320.197383