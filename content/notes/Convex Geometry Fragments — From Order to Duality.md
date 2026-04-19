---
title: "Convex Geometry Fragments — From Order to Duality"
draft: false
share: true
---

# Convex Geometry Fragments — From Order to Duality

> _Tags_: #convex-optimization #geometry #duality #norms #inequality  
> _Date_: 2026-04-13

---

## 1. Ordering Induced by a Cone

We begin with a quiet but powerful idea: **a cone defines an order**.

Given a cone $K$, define:

$$
x \le_K y ;; \Longleftrightarrow ;; y - x \in K
$$

### Geometric Taste

- If $K = \mathbb{R}_+^2$, then  
   $x - K$ is the **south-west region** anchored at $x$.
- If  
   $K = { x \in \mathbb{R}^2 \mid |x|_2 \le 1 }$ (unit ball),  
   then $x - K$ is a **disk centered at $x$ with radius 1**.

So the cone shapes what it means to be “below” a point. Not always downwards. Sometimes outward, sometimes radial.

---

## 2. Minimum vs Minimal Elements

### Minimum Element

If $x$ is a **minimum element** of $S$ w.r.t. $\le_K$, then:

> $x$ is the **unique minimizer** of  
> $$\lambda^T z$$  
> over $z \in S$, for any $\lambda \ge_{K^*} 0$.

---

### Minimal Element

If $x$ is a **minimal element**, then:

- It satisfies a weaker condition:

$$
\exists \lambda >_{K^*} 0 ;; \text{s.t.} ;;
x = \arg\min_{z \in S} \lambda^T z
$$

But:

- The reverse implication may fail
- Especially when **$S$ is not convex**

---

### Convexity Repairs the Gap

If $S$ is **convex**, then:

> Minimal ⇔ existence of supporting hyperplane  
> ⇔ linear functional minimization

Convexity quietly stitches equivalence back together.

---

## 3. Dual Cone — The Mirror Structure

The dual cone:

$$
K^* = { y \mid y^T x \ge 0,; \forall x \in K }
$$

### Geometric Interpretation

- $K^*$ is the **intersection of halfspaces**
- Each halfspace is defined by an **inward normal**
- All contain the origin

So $K^*$ is a kind of _shadow structure_ that encodes how $K$ can be “tested” linearly.

---

### Examples

- Dual of $\mathbb{R}_+^n$ is itself:
  $$
  (\mathbb{R}_+^n)^* = \mathbb{R}_+^n
  $$
- For PSD matrices:
  $$
  X, Y \in S_+^n ;\Rightarrow; \mathrm{tr}(XY) \ge 0
  $$

---

## 4. Order via Dual Cone

If $K$ is a **proper cone**, then:

$$
x \le_K y
;\Longleftrightarrow;
\lambda^T x \le \lambda^T y,
;\forall \lambda \ge_{K^*} 0
$$

### Intuition

Instead of checking $y - x \in K$,  
we check all **projections onto dual directions**.

> Order becomes: _no dual direction sees $x$ exceeding $y$_.

---

## 5. Dual Norm — Measuring via Inner Products

The dual norm:

$$
|z|_* = \sup_{|x| \le 1} z^T x
$$

And always:

$$
z^T x \le |z|_* \cdot |x|
$$

---

### Euclidean Norm

$$
\sup_{|x|_2 \le 1} z^T x = |z|_2
$$

Max achieved at:

$$
x = \frac{z}{|z|_2}
$$

So:

> Euclidean norm is self-dual.

---

### $\ell_\infty$ and $\ell_1$

$$
\sup_{|x|_\infty \le 1} z^T x
= \sum_{i=1}^n |z_i|
= |z|_1
$$

So:

> $\ell_\infty^* = \ell_1$

---

### General Case

If $1/p + 1/q = 1$, then:

$$
|\cdot|_p^* = |\cdot|_q
$$

A quiet reciprocity between norms.

---

### Matrix Case (A Hint of Spectral Structure)

For $Z \in \mathbb{R}^{m \times n}$:

$$
\sup_{|X|_2 \le 1} \mathrm{tr}(Z^T X)
= \sum_{i=1}^r \sigma_i(Z)
$$

This is the **nuclear norm**.

A bridge from operator norm to spectrum.

---

## 6. Jensen’s Inequality — Convexity in Expectation

For convex $f$:

$$
\mathbb{E}[f(x)] \ge f(\mathbb{E}[x])
$$

If $f$ is **strictly convex**, then:

$$
\mathbb{E}[f(x)] = f(\mathbb{E}[x])
;\Longleftrightarrow;
x \text{ is constant}
$$

Convexity punishes randomness.

---

## 7. Convex Functions — Second-Order View

### Scalar Case

If $f: \mathbb{R} \to \mathbb{R}$:

$$
f''(x) \ge 0 ;\Rightarrow; f \text{ is convex}
$$

---

### Vector Case

If $f: \mathbb{R}^n \to \mathbb{R}$:

$$
\nabla^2 f(x) \succeq 0
$$

Hessian must be **positive semidefinite**.

---

## 🌌 Closing Thread

A pattern quietly emerges:

- Cones define **order**
- Dual cones define **tests of order**
- Norms define **size**
- Dual norms define **maximal interaction**
- Convexity ensures **consistency between geometry and optimization**

It is as if every object carries a _dual voice_,  
and understanding comes from listening to both.
