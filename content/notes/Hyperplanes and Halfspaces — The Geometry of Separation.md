---
title: "Hyperplanes and Halfspaces — The Geometry of Separation"
date: 2026-03-28T11:00:00+08:00
draft: true
share: true
tags:
  - optimization
  - mathematics
categories:
  - machine learning theory
math: true
description: "Understanding hyperplanes and halfspaces — the fundamental geometric structures behind classification, separation theorems, and convex optimization"
---
---

# Hyperplanes and Halfspaces — The Geometry of Separation

## Overview

Hyperplanes and halfspaces are among the most important geometric objects in optimization and machine learning. They appear everywhere:

- **Support Vector Machines** — finding the optimal separating hyperplane
- **Linear classifiers** — decision boundaries defined by hyperplanes
- **Convex optimization** — feasible regions described by halfspace intersections
- **Separation theorems** — proving that disjoint convex sets can be separated

In this post, we build a complete understanding of hyperplanes and halfspaces, from their basic definition to their geometric interpretation and practical applications.

---

## 1. What is a Hyperplane?

### Formal Definition

A **hyperplane** is the set of points satisfying a set of nontrivial linear equations:

$$
\{x \in \mathbb{R}^n \mid a^T x = b,\ a \neq 0\}
$$

where:
- $a \in \mathbb{R}^n$ is the **normal vector**
- $b \in \mathbb{R}$ is the **offset** (or bias)

---

### What is "Nontrivial"? 

The "nontrivial" means the coefficients vector $a$ is not the zero vector.
Mathematically:
$$a\ne0$$
where $\mathbf{0} = (0,0,\dots,0)$ . This means at least one coefficient must be non-zero. 

---

### Why "Nontrivial"? Understanding $a \neq 0$

The nontrivial requirement of $a \neq 0$ is essential. 
It excludes cases where the equation is either:
- **Identically true** for all x (the whole space $\mathbb{R}^n$)
- **Never true** for any x (the empty set $\mathbb{\phi}$)
and ensures we actually get a **meaningful geometric object**, a proper hyperplane  
— a flat surface of dimension $n-1$.

Let's break down what happens when $a = 0$ corresponding the above two degenerate cases:

| Case              | Equation    | Result                                              |
| ----------------- | ----------- | --------------------------------------------------- |
| $a = 0, b = 0$    | $0^T x = 0$ | **Always true** — solution is all of $\mathbb{R}^n$ |
| $a = 0, b \neq 0$ | $0^T x = b$ | **Inconsistent** — no solution exists               |

Neither case produces a meaningful geometric object.

---

### Geometric Interpretation

The equation $a^T x = b$ has two components:

| Component         | Symbol | Geometric Meaning                                 |
| ----------------- | ------ | ------------------------------------------------- |
| **Normal vector** | $a$    | Direction perpendicular to the hyperplane         |
| **Offset**        | $b$    | How far the hyperplane is shifted from the origin |

The normal vector $a$ determines the "tilt" of the hyperplane,
while $b$ determines its position relative to the origin.
It's worthy to note that both $a$ and $-a$ determine the same hyperplane 
(as hyperplane is **unoriented**), but for halfspace, it doesn't.

---

## 2. Alternative Form: The Orthogonality View

There's another way to express the same hyperplane that reveals deeper geometric structure.
### Reformulation
Pick any point $x_0$ on the hyperplane (so $a^T x_0 = b$). Then:

$$
a^T x = b \quad \iff \quad a^T(x - x_0) = 0
$$
### Geometric Meaning
The condition $a^T(x - x_0) = 0$ says:
The vector from $x_0$ to any point $x$ on the hyperplane 
is **orthogonal** (perpendicular) to the normal vector $a$.
### Vector Space Notation
Using orthogonal complement notation, we can write the hyperplane as:
$$
x_0 + a^\perp
$$
where $a^\perp = \{v \mid a^T v = 0\}$ is the set of all vectors orthogonal to $a$.
**Interpretation:** The hyperplane is the point $x_0$ plus all directions perpendicular to $a$.

---

## 3. Examples Across Dimensions

Hyperplanes generalize the familiar concepts of lines and planes to any dimension.

| Dimension      | Hyperplane is...                | Equation                          |
| -------------- | ------------------------------- | --------------------------------- |
| $\mathbb{R}^1$ | A **point**                     | $ax = b$ (single solution)        |
| $\mathbb{R}^2$ | A **line**                      | $a_1 x_1 + a_2 x_2 = b$           |
| $\mathbb{R}^3$ | A **plane**                     | $a_1 x_1 + a_2 x_2 + a_3 x_3 = b$ |
| $\mathbb{R}^n$ | An $(n-1)$-**dimensional flat** | $a^T x = b$                       |

### Key Insight

A hyperplane in $\mathbb{R}^n$ always has dimension $n-1$. 
It's one dimension "less" than the ambient space.

---
## 4. Halfspaces: One Side of the Hyperplane

A hyperplane is like a wall in a room ($\mathbb{R}^n$) and 
it divides the room into two parts ---- **halfspaces**.
### Definition
Given a hyperplane $a^T x = b$ with $a \neq 0$:

| Halfspace                                 | Notation                 | Description                                  |
| ----------------------------------------- | ------------------------ | -------------------------------------------- |
| **Closed halfspace** <br>($-a$ direction) | $\{x \mid a^T x \le b\}$ | Points on one side, including boundary       |
| **Closed halfspace** <br>($+a$ direction) | $\{x \mid a^T x \ge b\}$ | Points on the other side, including boundary |
The hyperplane itself ($a^T x = b$) is the **boundary** shared by both halfspaces.

### Visual Intuition


<figure style="text-align: center;">
<pre style="display: inline-block; text-align: left; font-family: monospace; padding: 20px; line-height: 1.2; border: none; background: transparent; margin: 0;">
                    a (normal vector)
                    ↑
                    │
                    │
              {x | aᵀx ≥ b}
                    │
────────────────────────────────────────
            Hyperplane: aᵀx = b
────────────────────────────────────────
                    │
              {x | aᵀx ≤ b}
                    │
					↓
</pre>
</figure>
A hyperplane $a^T x = b$ separates space into two closed halfspaces. The normal vector $a$ points toward the halfspace where $a^T x \ge b$.


---
### Open vs. Closed Halfspaces

| Type                 | Definition               | Includes Boundary? |
| -------------------- | ------------------------ | ------------------ |
| **Closed halfspace** | $\{x \mid a^T x \le b\}$ | ✓ Yes              |
| **Open halfspace**   | $\{x \mid a^T x < b\}$   | ✗ No               |

In optimization, we typically work with **closed halfspaces** because they contain their boundary points (making them closed sets).

---

### Why the Normal Vector Points "Outward"

In the Visual Intuition subsection, we could see the direction vector always points outward.
We are going to dive little deep into it and explain why.



Take a point $x$ on the hyperplane ($a^T x = b$) and move in direction $a$:

$$
x' = x + t a \quad \text{for } t > 0
$$

Then:

$$
a^T x' = a^T(x + ta) = a^T x + t(a^T a) = b + t\|a\|^2 > b
$$

So moving in direction $a$ takes us into the $\ge$ halfspace.

**Key takeaway:** The normal vector $a$ points toward the **positive halfspace** $\{x \mid a^T x \ge b\}$.

---
## 6. Hyperplanes as Affine Sets

Hyperplanes are a special case of affine sets.

### Proposition

Every hyperplane is an **affine set**.

### Why?

Recall: A set $C$ is affine if it contains the entire line through any two points:

$$
x_1, x_2 \in C \implies \theta x_1 + (1-\theta)x_2 \in C,\ \forall \theta \in \mathbb{R}
$$

For a hyperplane $H = \{x \mid a^T x = b\}$:

If $a^T x_1 = b$ and $a^T x_2 = b$, then:

$$
a^T(\theta x_1 + (1-\theta)x_2) = \theta a^T x_1 + (1-\theta)a^T x_2 = \theta b + (1-\theta)b = b
$$

So the affine combination stays on the hyperplane.

---

### Affine Dimension of a Hyperplane

A hyperplane in $\mathbb{R}^n$ has **affine dimension $n-1$**.

| Space | Hyperplane Dimension |
|-------|---------------------|
| $\mathbb{R}^2$ | Line (dimension 1) |
| $\mathbb{R}^3$ | Plane (dimension 2) |
| $\mathbb{R}^n$ | $(n-1)$-dimensional flat |

---

## 7. Halfspaces are Convex Sets

### Proposition

Every halfspace is a **convex set**.

### Proof

Take the halfspace $H = \{x \mid a^T x \le b\}$. For any $x_1, x_2 \in H$ and $\theta \in [0, 1]$:

$$
a^T x_1 \le b \quad \text{and} \quad a^T x_2 \le b
$$

Consider the convex combination $\theta x_1 + (1-\theta)x_2$:

$$
\begin{aligned}
a^T(\theta x_1 + (1-\theta)x_2) &= \theta a^T x_1 + (1-\theta)a^T x_2 \\
&\le \theta b + (1-\theta)b \\
&= b
\end{aligned}
$$

So $\theta x_1 + (1-\theta)x_2 \in H$, proving convexity.

---

### Important Distinction

| Set Type | Convex? | Affine? |
|----------|---------|---------|
| Hyperplane | ✓ Yes | ✓ Yes |
| Halfspace | ✓ Yes | ✗ No |

Halfspaces are convex but **not affine** — they don't contain entire lines, only line segments.

---

## 8. Representation Theorem: Polyhedra

Hyperplanes and halfspaces are the building blocks of more complex shapes.

### Definition: Polyhedron

A **polyhedron** is the intersection of finitely many halfspaces (and possibly hyperplanes):

$$
P = \{x \mid Ax \le b,\ Cx = d\}
$$

where:
- $Ax \le b$ represents multiple halfspace constraints
- $Cx = d$ represents multiple hyperplane constraints

### Key Properties

- Polyhedra are always **convex sets**
- They generalize polygons (2D) and polyhedra (3D) to any dimension
- Linear programming feasible regions are polyhedra

---

## 9. Applications in Machine Learning

### Support Vector Machines (SVM)

The core idea of SVM is finding the **optimal separating hyperplane**:

$$
\text{Find } a, b \text{ such that:}
$$
$$
\begin{cases}
a^T x_i \ge b + 1 & \text{if } y_i = +1 \\
a^T x_i \le b - 1 & \text{if } y_i = -1
\end{cases}
$$

The goal: maximize the **margin** (distance between the two parallel hyperplanes).

---

### Linear Classifiers

A linear classifier makes decisions based on which side of a hyperplane a point lies:

$$
f(x) = \text{sign}(a^T x - b)
$$

- $f(x) = +1$: classify as positive class
- $f(x) = -1$: classify as negative class

The hyperplane $a^T x = b$ is the **decision boundary**.

---

### Separation Theorems

A fundamental result in convex analysis:

> **Separating Hyperplane Theorem**: If $C$ and $D$ are disjoint convex sets, there exists a hyperplane that separates them.

This theorem underlies:
- Duality theory in optimization
- Existence of classifiers
- Farkas' lemma in linear programming

---

## 10. Summary Table

| Concept | Definition | Dimension | Convex? | Affine? |
|---------|------------|-----------|---------|---------|
| **Hyperplane** | $\{x \mid a^T x = b\},\ a \neq 0$ | $n-1$ | ✓ Yes | ✓ Yes |
| **Closed Halfspace** | $\{x \mid a^T x \le b\},\ a \neq 0$ | $n$ | ✓ Yes | ✗ No |
| **Open Halfspace** | $\{x \mid a^T x < b\},\ a \neq 0$ | $n$ | ✓ Yes | ✗ No |

---

## 11. Geometric Summary

<figure>
<div style="text-align: center;">

**Hyperplane**: A flat "sheet" of dimension $n-1$ defined by $a^T x = b$

**Halfspace**: One side of a hyperplane, defined by $a^T x \le b$ or $a^T x \ge b$

**Normal vector $a$**: Perpendicular to the hyperplane, points toward the $\ge$ side

**Offset $b$**: Controls how far the hyperplane is from the origin

</div>
</figure>

---

## Conclusion

Hyperplanes and halfspaces are fundamental geometric objects that appear throughout optimization and machine learning:

1. **Hyperplanes** are flat surfaces defined by linear equations — they're both affine and convex
2. **Halfspaces** are the regions on one side of a hyperplane — they're convex but not affine
3. The **normal vector** determines orientation; the **offset** determines position
4. These structures form the building blocks for:
   - Linear classifiers and SVMs
   - Polyhedral feasible regions
   - Separation theorems in convex analysis

Understanding the geometry of hyperplanes and halfspaces provides intuition for more advanced topics like duality, KKT conditions, and convex separation theorems.

---

## Related Posts

- [Affine Sets, Convex Sets, Cones, and Closure](/posts/affine_convex_sets_cones_closure/) — Foundational concepts in convex geometry
- [Where the Name 'Lagrange Duality' Comes From](/posts/lagrange_duality_name_posted/) — Connecting geometry to optimization
