---
title: "Affine Sets, Convex Sets, Cones, and Closure"
date: 2026-03-28T18:28:25+08:00
draft: false
share: true
slug: "affine-sets-convex-sets-cones-and-closure"
math: true
---
## 1. Line and Line Segment

For points $x_1, x_2 \in \mathbb{R}^n$, let

$$
y = \theta x_1 + (1-\theta)x_2
$$
- If $\theta \in \mathbb{R}$, then $y$ lies on the **line** passing through $x_1$ and $x_2$.
- If $0 \le \theta \le 1$, then $y$ lies on the **line segment** between $x_1$ and $x_2$.

---

## 2. Affine Set

### Definition
If for any two distinct points $x_1, x_2 \in C$,
$$
\theta x_1 + (1-\theta)x_2 \in C,\quad \forall \theta \in \mathbb{R},
$$
then we say $C$ is an **affine set**.
### Interpretation

An affine set contains the **entire line** through any two of its points.

---

## 3. Affine Combination

Given points $x_1, \dots, x_k \in C$, an **affine combination** is

$$
\sum_{i=1}^k \theta_i x_i
$$
where $x_i \in C$ ,with the constraint

$$
\sum_{i=1}^k \theta_i = 1. $

### Important Fact

If $C$ is an affine *set*, then **any affine combination** of points in $C$ also belongs to $C$.

That is, if $x_1,\dots,x_k \in C$ and $\sum_{i=1}^k \theta_i = 1$, then

$$
\sum_{i=1}^k \theta_i x_i \in C. $

---

## 4. Affine Set as a Shifted Subspace

For an affine set $C$, choose some point $x_0 \in C$. Define

$$
V = C - x_0 = \{x - x_0 \mid x \in C\}.
$$
Then $V$ is a **linear subspace**, called the **direction space** of $C$.

So an affine set can be viewed as:

$$
C = x_0 + V.
$$
This means:
$V$ is the “direction/shape”
$x_0$ is the “offset/location”

So an affine set is essentially a **linear subspace shifted away from the origin**.

---

## 5. Proof that $V = C - x_0$ is a Linear Subspace

We want to show $V$ is closed under:**addition** and **scalar multiplication**

Take $v_1, v_2 \in V$. 
Then by definition of V, we have
$$
v_1 + x_0 \in C,\qquad v_2 + x_0 \in C.
$$
For any scalars $\alpha, \beta \in \mathbb{R}$,
$$
\alpha v_1 + \beta v_2 + x_0
$$
can be rewritten as
$$
\alpha(v_1 + x_0) + \beta(v_2 + x_0) + (1-\alpha-\beta)x_0.
$$
Since
$$
\alpha + \beta + (1-\alpha-\beta) = 1,
$$
this is an **affine combination** of points in $C$. Therefore,
$$
\alpha v_1 + \beta v_2 + x_0 \in C.
$$
So
$$
\alpha v_1 + \beta v_2 \in V.
$$
Hence $V$ is closed under **addition** and **scalar multiplication** , therefore  ***linear subspace**.

---

## 6. Subspace vs Affine Set

### A subspace must satisfy:
- It **passes through the origin**
- It is **closed under addition**
- It is **closed under scalar multiplication**
So:
- **Subspace** = linear structure through the origin
- **Affine set** = shifted linear structure
---

## 7. Example: A Line as an Affine Set

A line through $x_1$ and $x_2$ can be written as
$$
x_2 + \theta(x_1 - x_2),\quad \theta \in \mathbb{R}.
$$
This is an affine set.
If we let
$$
v = x_1 - x_2,
$$
then the associated direction space is
$$
V = {\theta(x_1 - x_2) \mid \theta \in \mathbb{R}}.
$$
This is a **1-dimensional subspace**.
If $x_2 = 0$, then the affine set passes through the origin and becomes a subspace.

---

## 8. Why Does $\sum \theta_i = 1$ Matter in convex constraint?
Geometrically,the constraint
$$
\sum_{i=1}^k \theta_i = 1
$$
ensures that the combination stays inside the convex structure. More precisely, we can think of a convex combination as **weighted average** of points. If the sum of coefficients is one, then everything is fine and the points stays exactly on the **line** or inside the **shape**. If it's smaller than one, it turns out to be shorter or narrow meaning it can't reach these points. In contrast, if it's larger than one, it will run outside the line or shape. Let's take a two dimension as an example. 
	If Σθᵢ = 1:                     If Σθᵢ = 0.8:                         If Σθᵢ = 2:
    
x₁ ●━━━━●━━━━ ● x₂     x₁ ●   ━━●━━  ● x₂                   ━━━x₁●━━━━●━━━━●x₂━━━━ 
         ↑                               ↑                                                                       ↑
      y stays                         y stays                                                            y shoots
      between                    inside                                                                beyond
here, we us the '━' denote range where the convex combination could reach.

---

# 9. Affine Hull

## Definition

For any set $C$, the **affine hull** of $C$, denoted $\operatorname{aff}(C)$, is the **smallest affine set** containing $C$

Equivalently,
$$
\operatorname{aff}(C) \cap {A \mid A \text{ is affine}  \text{ and }C \subseteq A}.
$$
We could also see it as the **intersection of all affine supersets** of $C$.

### Geometric Meaning

It is the “smallest flat shape” containing $C$.

Examples:

- Two distinct points → affine hull is the **line** through them
    
- Three non-collinear points in $\mathbb{R}^2$ → affine hull is the **plane**
    
- A single point → affine hull is just that **point**
    

### Clarification

> Is affine hull a specific shape like a “ball”?

**No.**  
Affine hull is not a rounded object like a ball. It is always a **flat geometric object**:

- point
    
- line
    
- plane
    
- hyperplane
    
- full space
    

It is about **flatness**, not curvature.

---

## 10. Affine Dimension

The **affine dimension** of a set $C$ is defined as:

$$
\dim(\operatorname{aff}(C)).
$$
That is, the dimension of its affine hull.

Examples:

- A line in $\mathbb{R}^3$: affine dimension = 1
    
- A plane in $\mathbb{R}^3$: affine dimension = 2
    
- A full 3D body in $\mathbb{R}^3$: affine dimension = 3
    

---

# 11. Convex Combination

Based on affine combination, if we add another constraint

$$
\theta_i \ge 0,
$$
then we get a **convex combination**:

$$
\sum_{i=1}^k \theta_i x_i
$$
subject to

$$
\sum_{i=1}^k \theta_i = 1,\qquad \theta_i \ge 0. $

### Geometric Meaning

A convex combination stays **inside the shape spanned by the points**, without overshooting.

- Affine combination → can go outside
    
- Convex combination → stays inside
    

---

# 12. Convex Hull

## Definition

The **convex hull** of $C$, denoted $\operatorname{conv}(C)$, is:

> the **smallest convex set** containing $C$

Equivalently,

$$
\operatorname{conv}(C)

\bigcap {K \mid K \text{ is convex and } C \subseteq K}.  
$$
It is the **intersection of all convex supersets** containing $C$. ### Intuition If affine hull is the “smallest flat sheet” containing the points, then convex hull is the **smallest rubber-band shape** wrapping around them. ---

# 13. Convex Combination in More General Forms The idea of convex combination can be generalized beyond finite sums. For example, in probabilistic form: If $x_i \in C$ and $p_i \ge 0$ with

$$
\sum_i p_i = 1,  
$$
then
$$
\sum_i p_i x_i \in C.  
$$
This is just a convex combination with probabilistic weights. More generally, with a distribution $p(x)$,
$$
\int x, p(x), dx  
$$
can be thought of as a continuous convex combination (under suitable conditions). ### Applications This idea appears in: - probability - expectations - mixtures - optimization - machine learning ---

# 14. Convex Cone A **convex cone** can be thought of geometrically as a shape with: - an **apex** at the origin - extending outward in certain directions It is like taking rays and allowing all nonnegative scaling + convex mixing. ---

# 15. Conic Combination Starting from convex combination, if we **remove the constraint**

$$
\sum_i \theta_i = 1,  
$$
but still require
$$
\theta_i \ge 0,  
$$
then we get a **conic combination**:
$$
\sum_{i=1}^k \theta_i x_i,\qquad \theta_i \ge 0.  
$$

---

# 16. Conic Hull The **conic hull** of $C$ is: #

$$
\operatorname{cone}(C) \left{ \theta_1 x_1 + \cdots + \theta_k x_k ;\middle|; \theta_i \ge 0,\ x_i \in C,\ i=1,\dots,k \right}.
$$
It is the set of all **conic combinations** of points in $C$.

Equivalently, it is:

> the **smallest convex cone** containing $C$

or

$$
\operatorname{cone}(C)

\bigcap {K \mid K \text{ is a convex cone and } C \subseteq K}.  
$$

---

# 17. Summary: Hulls ## Geometric Definitions ### Affine Hull

$$
\operatorname{aff}(C)  
$$
= intersection of all **affine supersets** containing $C$ ### Convex Hull

$$
\operatorname{conv}(C)  
$$
= intersection of all **convex supersets** containing $C$ ### Conic Hull

$$
\operatorname{cone}(C)  
$$
= intersection of all **convex cones** containing $C$ ### Important Note They are all **unique** for a given set $C$. ---

# 18. Closure and Closed Sets The main issue in whether a set $C$ is **closed** is its **boundary**. ## Intuition - If a set contains its boundary, then it is **closed** - If it does not contain its boundary, then it is **not closed** The **closure** of a set is: > the original set plus all of its boundary / limit points It is the **smallest closed set** containing the original set. ---

## Formal Idea The closure of $C$, denoted $\overline{C}$, is:

$$
\overline{C} = C \cup {\text{limit points of } C}.  
$$

---

## Important Note Closure is a **topological concept**. It applies to **any set** in a topological space, regardless of whether it is: - finite - infinite - countable - uncountable The key mechanism is: > **limit points** That’s the hidden engine behind “closedness”. ---

# 19. Dimension of a Subspace If $V$ is a linear subspace, then

$$
\dim(V)  
$$
is the number of vectors in a basis of $V$. Examples: - A line through the origin → dimension 1 - A plane through the origin → dimension 2 - $\mathbb{R}^n$ → dimension $n$ ---

# 20. Final Core Distinction ## Affine Set Closed under **affine combinations**:

$$
\sum_i \theta_i x_i,\qquad \sum_i \theta_i = 1  
$$

## Convex Set Closed under **convex combinations**:

$$
\sum_i \theta_i x_i,\qquad \sum_i \theta_i = 1,\ \theta_i \ge 0  
$$

## Convex Cone Closed under **conic combinations**:

$$
\sum_i \theta_i x_i,\qquad \theta_i \ge 0  
$$

## Linear Subspace Closed under: - addition - scalar multiplication and must pass through the **origin**. ---

# 21. One-Screen Summary Table |Concept|Coefficients Condition|Geometric Meaning| |---|---|---| |**Affine combination**|$\sum_i \theta_i = 1$|Flat combinations| |**Convex combination**|$\sum_i \theta_i = 1,\ \theta_i \ge 0$|Inside the shape| |**Conic combination**|$\theta_i \ge 0$|Positive scaling / cone| |**Affine hull**|all affine combinations|Smallest affine set| |**Convex hull**|all convex combinations|Smallest convex set| |**Conic hull**|all conic combinations|Smallest convex cone| |**Subspace**|closed under all linear combinations|Passes through origin| ---

# 22. Very Short Intuition Summary You can think of these four as a little **constraint ladder**: ## Linear Subspace Can do **any linear combination**. ## Affine Set Can do combinations with

$$
\sum_i \theta_i = 1  
$$

## Convex Set Can do combinations with

$$
\sum_i \theta_i = 1,\quad \theta_i \ge 0  
$$

## Convex Cone Can do combinations with

$$
\theta_i \ge 0  
$$
So they are like four different “recipes” for mixing points.






