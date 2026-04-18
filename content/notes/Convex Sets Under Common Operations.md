---
title: "Convex Sets Under Common Operations"
date: 2026-04-07
draft: true
share: true
tags:
  - math
  - convex-optimization
  - convexity
  - linear-algebra
  - optimization
summary: "Understanding how convexity is preserved under intersections, affine maps, perspective functions, and generalized inequalities."
aliases:
  - Convexity Under Operations
  - 凸集在常见操作下的保持性
---
# Convex Sets Under Common Operations

One of the most useful habits in convex optimization is this:

> Instead of proving a set is convex from scratch every time, try to express it as the result of **operations that preserve convexity**.

This viewpoint is extremely powerful.  
Once internalized, many seemingly complicated convex sets become structurally obvious.

In this note, I introduce several operations that preserve convexity ：

- intersection
- affine image / inverse image
- perspective function
- linear-fractional function
- generalized inequality

---

## 1. Intersection Preserves Convexity

A family of sets can be written as

$$
\bigcap_{\alpha \in A} S_\alpha
$$

where:

- $A$ is the **index set**
- $\alpha$ is an index in $A$
- each $S_\alpha$ is a set

The symbol $\bigcap$ means taking the **intersection** of a collection of sets.

### Key fact

If every $S_\alpha$ is convex, then

$$
\bigcap_{\alpha \in A} S_\alpha
$$

is also convex.

This is one of the most basic but most frequently used tools in convex analysis.

### Example: Halfspaces and Polyhedra

A **halfspace** can be either:

- **closed**, if it includes the boundary hyperplane
- **open**, if it does not include the boundary hyperplane

A **polyhedron** can be viewed as the intersection of finitely many halfspaces and hyperplanes.

So rather than proving directly that a polyhedron is convex, we can simply say:

1. halfspaces are convex  
2. hyperplanes are convex  
3. intersection preserves convexity  

Therefore, polyhedra are convex.

This style of reasoning appears everywhere in convex optimization.

---

## 2. Affine Functions Preserve Convexity

An **affine function** has the form

$$
f(x)=Ax+b
$$

This includes many familiar transformations:

- **scaling**  
- **translation**
- **projection**
- coordinate selection
- linear combinations

### Key fact

If $S$ is convex, then its image under an affine function is also convex.

That is, if $f(x)=Ax+b$ and $S$ is convex, then

$$
f(S)=\{Ax+b \mid x\in S\}
$$

is convex.

This is conceptually important because many convex sets are not described directly in their “final coordinates”, but instead arise as affine transformations of simpler convex sets.

### Cartesian Product

Another common construction is the **Cartesian product**, which refers to ordered tuples formed by taking one element from each set.

For example, if

$$
x \in C_1,\qquad y\in C_2
$$

then

$$
(x,y)\in C_1\times C_2
$$

If both $C_1$ and $C_2$ are convex, then their Cartesian product is also convex.

---

## 3. Perspective Function

The **perspective function** is one of the most important operations in convex optimization.

It is defined as

$$
P:\mathbb{R}^{n+1}\to\mathbb{R}^n
$$

with

$$
P(x,t)=\frac{x}{t}
$$

and domain

$$
\operatorname{dom}P=\mathbb{R}^n\times\mathbb{R}_{++}
$$

where $t>0$.

---

## 4. Geometric Intuition of the Perspective Map

A useful way to think about the perspective map is this:

> It **normalizes the last coordinate to 1**.

For example, in $\mathbb{R}^3$,

$$
\chi=(x_1,x_2,x_3)
$$

then

$$
P(\chi)=\left(\frac{x_1}{x_3},\frac{x_2}{x_3},\frac{x_3}{x_3}\right)
=
\left(\frac{x_1}{x_3},\frac{x_2}{x_3},1\right)
$$

So after applying the map, the last coordinate becomes constant.  
In many contexts, we simply drop that final coordinate and only keep

$$
\left(\frac{x_1}{x_3},\frac{x_2}{x_3}\right)
$$

This is why the perspective function often feels like a **projection after normalization**.

---

## 5. Why the Perspective Image of a Convex Set Is Convex

Let

$$
C\subseteq \mathbb{R}^n\times\mathbb{R}_{++}
$$

and define

$$
P(C)=\left\{
\frac{\tilde{x}}{x_{n+1}}
\;\middle|\;
(\tilde{x},x_{n+1})\in C
\right\}
$$

We want to show that if $C$ is convex, then $P(C)$ is convex.

Take two points in $C$:

$$
x=(\hat{x},x_{n+1}),\qquad
y=(\tilde{y},y_{n+1})
$$

Then

$$
P(x)=\frac{\hat{x}}{x_{n+1}},
\qquad
P(y)=\frac{\tilde{y}}{y_{n+1}}
$$

Now consider a convex combination:

$$
P(\theta x+(1-\theta)y)
=
\frac{\theta \hat{x}+(1-\theta)\tilde{y}}
{\theta x_{n+1}+(1-\theta)y_{n+1}}
$$

Rearranging gives

$$
P(\theta x+(1-\theta)y)
=
\mu P(x)+(1-\mu)P(y)
$$

where

$$
\mu=
\frac{\theta x_{n+1}}
{\theta x_{n+1}+(1-\theta)y_{n+1}}
$$

Since $\mu\in[0,1]$, this is again a convex combination of $P(x)$ and $P(y)$.

So $P(C)$ is convex.

### The deeper pattern

Notice what happened here:

> A convex combination **before** applying $P$ becomes another convex combination **after** applying $P$,  
> but with a **different weight**.

That is the algebraic heart of the perspective trick.

---

## 6. Inverse Image Under the Perspective Map

Now define the inverse image:

$$
P^{-1}(C)=
\left\{
(\tilde{x},x_{n+1})
\;\middle|\;
\frac{\tilde{x}}{x_{n+1}}\in C
\right\}
$$

To show $P^{-1}(C)$ is convex, take

$$
(x,t),(y,s)\in P^{-1}(C)
$$

and let $\theta\in[0,1]$.

We need to show

$$
\theta(x,t)+(1-\theta)(y,s)\in P^{-1}(C)
$$

That is,

$$
(\theta x+(1-\theta)y,\ \theta t+(1-\theta)s)\in P^{-1}(C)
$$

which means we need

$$
\frac{\theta x+(1-\theta)y}
{\theta t+(1-\theta)s}
\in C
$$

But in fact,

$$
\frac{\theta x+(1-\theta)y}
{\theta t+(1-\theta)s}
=
\mu \frac{x}{t}+(1-\mu)\frac{y}{s}
$$

where

$$
\mu=
\frac{\theta t}{\theta t+(1-\theta)s}
$$

So this is a convex combination of two points already in $C$.

Hence, if $C$ is convex, then $P^{-1}(C)$ is convex.

---

## 7. Linear-Fractional Functions

A **linear-fractional function** can be written as the composition of:

1. an affine function
2. the perspective function

Let

$$
g(x)=
\begin{bmatrix}
A\\
c^\top
\end{bmatrix}x
+
\begin{bmatrix}
b\\
d
\end{bmatrix}
$$

and let

$$
P(x,t)=\frac{x}{t}
$$

Then define

$$
f=P\circ g
$$

This gives the familiar form

$$
f(x)=\frac{Ax+b}{c^\top x+d}
$$

as long as the denominator is positive.

### Why this matters

This decomposition is extremely useful because it says:

> A linear-fractional map is not mysterious.  
> It is just **affine transformation + perspective transformation**.

So once you understand those two pieces, you understand why many linear-fractional constructions preserve convexity.

---

## 8. Generalized Inequality

A **generalized inequality** induced by a cone $K$ is defined as

$$
x \preceq_K y
\iff
y-x\in K
$$

The associated **strict generalized inequality** is

$$
x \prec_K y
\iff
y-x\in \operatorname{int}K
$$

This gives a way to define “$\leq$-like” structure in spaces much richer than $\mathbb{R}$.

---

## 9. Important Special Cases

### Case 1: $K=\mathbb{R}_+$

Then

$$
x\preceq_K y
$$

is exactly the ordinary ordering

$$
x\le y
$$

on $\mathbb{R}$.

### Case 2: $K=\mathbb{R}_+^n$

Then generalized inequality becomes **componentwise comparison**:

$$
x\preceq_K y
\iff
x_i\le y_i,\quad i=1,\dots,n
$$

This is the vector version of “less than or equal to”.

### Case 3: $K=S_+^n$

Then for symmetric matrices,

$$
X\preceq_K Y
\iff
Y-X\succeq 0
$$

which means that $Y-X$ is **positive semidefinite**.

This is the matrix analogue of ordering.

Because the last two cases appear so frequently, people often omit the subscript and simply write

$$
\preceq,\qquad \prec
$$

with the meaning determined by context.

---

## 10. Generalized Inequality as a Partial Order

Generalized inequality is usually a **partial ordering**, not a total ordering.

This distinction matters a lot.

In $\mathbb{R}$, every two numbers are comparable.  
But in higher-dimensional spaces, many elements are **not comparable** under the induced order.

That is exactly why optimization over cones feels different from ordinary scalar optimization:  
the order structure itself becomes geometric.

---

## 11. Properties of Relations

To discuss orderings properly, we need several relation properties.

### Reflexive

A relation is **reflexive** if every element is related to itself.

### Symmetric

A relation is **symmetric** if

$$
\forall a,b\in C,\quad aRb\Rightarrow bRa
$$

### Antisymmetric

A relation is **antisymmetric** if

$$
\forall a,b\in C,\quad aRb\ \text{and}\ bRa\Rightarrow a=b
$$

### Asymmetric

A relation is **asymmetric** if

$$
\forall a,b,\quad aRb\Rightarrow \neg(bRa)
$$

These are not just abstract definitions.  
They determine what kind of “ordering language” is actually valid in optimization.

---

## 12. Minimum vs Minimal

This distinction is easy to overlook but conceptually important.

### In a linear order

Under a **linear ordering**, **minimum** and **minimal** are the same.

### In a partial order

Under a **partial ordering**, they are different.

A **minimum** element:

- is less than or equal to **every** element
- is therefore unique if it exists
- may not exist

A **minimal** element:

- only needs that **no other element is strictly smaller**
- does **not** need to be comparable to every element
- may have **more than one**

This is one of those distinctions that looks tiny on paper but becomes very real once you start thinking geometrically.

In partial orders, the landscape is no longer a single downhill slope.  
It becomes a mountain range with several local ledges.

---

## Closing Thought

A large part of convex optimization becomes simpler once you stop asking:

> “Is this set convex?”

and instead ask:

> “Can I express this set using operations that preserve convexity?”

That shift is small in wording but huge in power.

It turns convexity from something you repeatedly prove by hand into something you **recognize structurally**.

---

## Related Concepts

- convex sets
- affine hull
- halfspace / hyperplane
- polyhedron
- perspective transformation
- linear-fractional function
- cone-induced ordering
- semidefinite ordering