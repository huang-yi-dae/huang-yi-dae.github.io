---
title: "Where the Name 'Lagrange Duality' comes from"
date: 2026-03-04T09:31:47+08:00
draft: true
share: true
tags:
  - optimization
  - machine learning
  - convex optimization
categories:
  - machine learning theory
math: true
description: "Understanding Lagrange duality from geometric intuition to the dual problem"
---
# Overview

In machine learning, many optimization problems involve **constraints**. 
Examples include SVM margin constraints, probability simplex constraints, 
or regularization constraints.

Lagrange duality provides a powerful framework to handle these problems.

Conceptully, is does two important things:

1. **Merge constraints into objective function**  using *Lagrange multipliers*
2. **Construct another optimization problem** (the *dual problem*) by exchanging the order of minimization and maximization.

The dual problem often has useful properties:
- sometimes **easier to solve**
- reveals **important structure** (e.g., the kernel trick in SVM)

In this post we gradually build the idea of Lagrange duality starting from the geometric intuition of constrained optimization.

# 1. Geometry of Constrained Optimization 

Consider a constrained optimization problem
$$
\min_w f(w)
$$
subject to
$$
h(w) = 0
$$
Geometrically:
- The constraint defines a **surface**.
- We must find the minimum **restricted to that surface**

We could image the process of minimization is trying to find the lowerest point of a valley and the constraints(**surface**) is the fence on the valley that limits our actions.

When we get to the bottom of the valley, aka at the optimal point \( w^* \), we cannot move along the surface to further decrease \( f \).

This implies an important geometric condition(you should be able to picture it in your mind):

> The gradient of \( f \) must be orthogonal to the feasible surface.

Since the gradient of \(h\) is also normal to the constraint surface, the two gradients must be **aligned**.

This gives:
$$
 \nabla f(w^*) = \beta \nabla h(w^*) 
$$

This condition is the core idea behind the **Lagrange multiplier method**, introduced by the 18-century mathematician ** Joseph-Louis Lagrange**.

# 2. From Constraints to Penalties 

Let's shift our view form geometry to formula.

Suppose we want to sulve:
$$
min_w f(w)
$$
subject to:
$$
h(w) = 0
$$
The difficulty comes from the constraint:
we cannot choose w freely.

A natural idea is :
*Since the constrainted problem is hard, can we transform it into an unconstrainted one *

One simple approach is to *penalize constraint violations*.

For exapmle, consider
$$
f(w) + \rho \left| h(w) \right| 
$$

where $ \rho $ is a large constant.

Interpretation:
- if $h(w) = 0 $ -> no penalty
- if $ h(w) \ne 0 $ -> objective increases
If $ \rho $ is sufficiently large, the optimizer will prefer points satisfying the constraint.

However, this approch has a problem:
- choosing the correct penalty strength $ \rho $ is difficult
- too small -> constraint ignored
- too large -> optimization unstable

So instead of fixing the penalty weight, we can treat is as a variable which allow the optimization procedure adjust it automatically.

## From Fixed Penalty to Adaptive Penalty

To achieve this, we repalce the fixed coefficent $ \rho $ with a variable $ \beta $.
$$
f(w) + \beta h(w) 
$$
Here $ \beta $ is not predetermined. 
Instead, it is *free to change depending on the candiate solution* w. 
This means the penalty can *adapt to the degree of constraint violation*. 

To see why this works, imagine the following interaction:
- We propose a candiate solution w.
- Then the multiplier $ \beta $ is chosen to make the objecive as large as possibe.

If the constraint is violated, meaning 
$$ 
h(w) \ne 0 
$$
Then $ \beta $ can increase the objective arbitrarily by choosing a large magnitude.
For example:
- if $ h(w) > 0 $, choose $ \beta \to +\infty $
- if $ h(w) < 0 $, choose $ \beta \to -\infty $
In both case, the objective becomes extremely large.
Thus any point violating the constraint becomes infinitely undesirable.

On the other hand, if 
$$
h(w) = 0
$$
then the penalty term disappers:
$$
\beta h(w) = 0
$$
and the multiplier has no effect.

Therefore the optimization naturally prefers feasible points (because unfeasible points is being 
automatically penalized).

## The Lagrangian

This reasonging leads naturally to the *Lagrangian function*
$$
L(w,\beta) = f(w) + \beta h(w)
$$
where $ \beta $ is called a *Lagragian multipler*.

Instead of fixing the penalty strength in advance, 
we allow the multiplier to adjust automatically so that constraint violations are heavily penalized.

### . How Is the Penalty Chosen Automatically?

Previously, we introduced the *Lagrangian* 
which allow 'penalty strength' $ \beta $ to be any value to adaptively penalize, 
but we have not yet explained how this penalty coefficient is chosen.

Let's first briefly explain it here.
The key idea is that the multiplier is chosen after we propose a candiate solution $ w $.
More precisely, consider the following procedure:
- 1. We propose a candiate solution $ w $.
- 2. The the multiplier $ \beta $ choose a value that *maximizes the Lagranian*.
Mathematically,
$$
\max_{\beta} L(w, \beta) 
$$


### Extending to Inequlity Constraint

So far, we only consider the equality constraints which is simple.
However, most machine learning problems involve *inequality constraints*.
$$ 
g_i(w) \le 0
$$
Thus, the problem change to:
$$
\min_w f(w)
$$
subject to
$$
\begin{align}
 g_i(w) &\le 0 \\
 h_i(w) &= 0
\end{align}
$$
To incorporate them, we extend the Lagrangian to:
$$
L(w,\alpha, \beta) =  f(w) + \sum_i \alpha_i g_i(w) + \sum_i \beta_i h_i(w) 
$$
where the multipliers satisfy:
$$
\alpha \ge 0
$$
The non-negativity constraint ensures that violation of:
$$ 
g_i(w) \le 0
$$
increase the objective value.
Thus inquality constraints can also be handled through the same adaptive penalty mechanism.


### General Lagrangian

In last section, we constructed Generalized Lagrange Function:
$$
L(w, \alpha, \beta) = f(w) + \sum \alpha_i g_i(w) + \sum \beta_i h_i(w)
$$
with:
$$
\alpha_i \ge 0
$$
You could view is as :
Original objective + penality for violating constraints
The multiplier $ \alpha $ and $ \beta $ *how strong constraint violations are penalized*.

We pay attention to two designs firstly:

- (1) Inequality Constraint
$$ 
\alpha_i g_i(w) 
$$
with limitation:
$$ 
\alpha_i \ge 0 
$$
Let's talk about this condition in case:

if:
$$
g_i(w) > 0 
$$
which means violating constraints.
thus:
$$ 
\alpha_i g_i(w) > 0 
$$
and this will increase value of objective function(L).
The key idea here is: 
$ g_i(w) $ determines how much violates
$\alpha$ determines 'Penality intensity'
So, $ \alpha $ is the 'scaler' that controls magnification of penality.

if:
$$ 
g_i(w) = 0 
$$	
exactly on the edge of that.
then:
$$ 
\alpha_i g_i(w) = 0 
$$
In this situation, no matter how large $ \alpha $ is, it make no difference.
And this is key reason for appearance of 'Support Vector'. 

if:
$$ 
g_i(w) < 0 
$$
then:
$$ 
\alpha_i g_i(w) < 0 
$$
that means, this term will decrease L.

One more point to notice is: we will max L with $ \alpha $ in later section.
So if:
$$
g_i(w) < 0 
$$
increasing $ \alpha $ will only make L smaller.
In this case, best choice of maximization is let:
$$
\alpha_i = 0 
$$
This explains: 
why only points on the boundary can have $ \alpha > 0 $ 

Detailed explained: Under the premise that $ g_i(w) $ satisfies constraints,
$ g_i(w) $ could only be equal to or less than zero. 
And we have to max L with $ \alpha $(later we will discuss this further and detailed). 
When $ g_i(w) < 0 $, to max L we need to guarteen $ \alpha $ is as small as possible, 
that's to say $ \alpha = 0 $ is the best choice. 
When $ g_i(w) = 0 $, $ \alpha $ make no inflence on L, so it could be larger than zero.


- (2) Equality Constraint

For equality constaint:
$$ 
h_i(w) = 0 
$$
We add this term:
$$ 
\beta_i h_i(w) 
$$
You may  have notice one key difference:
There is no sign limitation for $ \beta $

Let's discuss about is in cases:
if:
$$ 
h_i(w) \ne 0 
$$
then $ \beta $ could total make this term arbitrarily large regardless
of value of $ h_i(w) $
That's why:
if:
$$ 
h_i(w) \ne 0 
$$
we could make 
$$ 
|\beta_i| \to \infty 
$$
thus:
$$ 
L \to \infty 
$$
if:
$$ 
h_i(w) = 0 
$$
It's obvious that $ \beta $ has no effect at all.

After discussing about the two penalty term, 
we have a deep understanding of mechanism of how $ \alpah, \beta$ penalize 
and konw that they control magnifications of penalization.

But this raises an important question:
*How are these penalty strengths chosen?*
Before, we have discuss and come to a conclusion that: manually choose and predetermined it is hard and
no flexible enough. We mentioned that we will *let the penalty strengths be chosen automatically by optimization itself*.In next section, we will give out how this happens in detailed.


### Automatic Selection of Penalty Strength

Consider the Lagrangian:
$$ 
L(w, \alpha,\beta) 
$$
Insteading of fixing the multipliers beforehand, we allow them to be chosen after *a candiate solution* w *is proposed*
More precisely, for a give w, we compute:
$$
\max_{\alpha, \beta:\alpha_i \ge 0} L(w, \alpha, \beta)
$$
This means the multipliers choose values that *maxmize the Lagrangian*

Intuitively:
- $ w $ tries to minimize the objective
- multipliers try to *increase the objective whenever constraints are violated*
Thus the multipliers act as an adversary that exposes constraint violations.


What Happens for Different Points?

Case 1: feasible point

If
$$
g_i(w) \leq 0,\quad h_i(w) = 0
$$

then
$$
L(w,\alpha,\beta) = f(w) + \sum_i \alpha_i g_i(w)
$$

Since
$$
\alpha_i \geq 0
$$
and
$$
g_i(w) \leq 0
$$

we have
$$
\alpha_i g_i(w) \leq 0
$$

Thus the largest value occurs when
$$
\alpha_i = 0
$$

so
$$
\max_{\alpha,\beta} L(w,\alpha,\beta) = f(w)
$$

Case 2: inequality constraint violated

If
$$
g_i(w) > 0
$$

then increasing $\alpha_i$ increases the objective:
$$
\alpha_i g_i(w) \to \infty
$$

Therefore
$$
\max_{\alpha,\beta} L(w,\alpha,\beta) = \infty
$$

Case 3: equality constraint violated

If
$$
h_i(w) \neq 0
$$

then choosing large $|\beta_i|$ makes
$$
\beta_i h_i(w) \to \infty
$$

so again
$$
\max_{\alpha,\beta} L(w,\alpha,\beta) = \infty
$$

Result: Automatic Penalty Mechanism

Therefore

$$
\max_{\alpha,\beta:\alpha_i \geq 0} L(w,\alpha,\beta)
$$

produces exactly the function

$$
\theta(w) =
\begin{cases} 
f(w) & \text{if feasible} \\
\infty & \text{otherwise}
\end{cases}
$$

This means the constrained optimization problem
$$
\min_{w} f(w)
$$
subject to constraints

is equivalent to
$$
\min_{w} \max_{\alpha,\beta:\alpha_i \geq 0} L(w,\alpha,\beta)
$$

This formulation reveals an intresting interpretation.
The optimization becomes a *two-player game*:
- the variable w tries to minimize the objective
- the multipliers try to maxmize the penalty for violations
So primal, constrainted problem becomes a min-max structure problem, which is
the starting point for *Lagrangian duality*

In next section, we will see that how exchanging the order of optimization leads to
a new optimization problem called the *dual problem*.


# 3. From the Min-Max Formulation to the Dual Problem

We previously showed that the constrainted optimization problem can be written as:
$$ 
\min_w \max_{\alpha,\beta : \alpha_i \ge 0} L(w, \alpha, \beta) 
$$
By this, constraints optimization becomes a min-max game.
But now, an intresting question arises.
*What happens if we reverse the order of optimization*
Insteading of sloving:
$$ 
\min_w \max_{\alpha,\beta} L(w, \alpha, \beta) 
$$
consider:
$$ 
\max_w \min_{\alpha,\beta} L(w, \alpha, \beta) 
$$
This lead to a new optimization problem.

## The Dual Function

Suppose we fix the multipliers $ \alpha, \beta $.
For these fixed penalty coefficients, we can minimize the Lagrangian over w:
$$
\theta_D(\alpha, \beta) = \min_w L(w, \alpha, \beta)
$$
This function is called the *dual function*
Interpretation:
- the multipliers define a particular penalty rule
- given that rule, we compute the best possible objective value
Intutively, the multipliers define a particular penalty rule, and the dual function asks:
*Given this penalty rule, what's the smallest value the objective can achieve*

## The Dual Function Gives a Lower Bound

An important observation is that the function always provide a *lower bound* on the optimal
value of the original problem. 
To see why, consider any feasible point $ w $ satisfying:
$$
g_i(w) \le 0, h_i(w) = 0
$$
Then the Lagrangian becomes:
$$
L(w, \alpha, \beta) =  f(w) + \sum_i \alpha_i g_i(w) 
$$
Since
$$
\alpha_i \ge 0, g_i(w) \le 0
$$
we have
$$
\alpha_i g_i(w) \le 0
$$
Thus
$$
L(w, \alpha,\beta) \le f(w)
$$

Now recall that the dual function takes the *minimum over all* w:
$$
\theta_D(\alpha, \beta) = \min_w L(w, \alpha, \beta)
$$
Therefore
$$
\theta_D(\alpha, \beta) \le L(w, \alpha, \beta)
$$
Combining the two inequalties gives:
$$
\theta_D(\alpha, \beta) \le f(w)
$$
for every feasible w.

In particular, if $ p^* $ denotes the optimal value of the primal problem, we obtain
$$
\theta_D(\alpha, \beta) \le  p^*
$$
Thus, every choice of multipliers produces a valid lower bound on the optimal objective value.

## Choosing the Tightest Bound

Different multipliers produce different lower bounds.
Some choices give very loose bounds, while others may give much better exitmates.
Since all these bounds are valid the natural strategy is to choose the *largest possible one*
Therefore, we solve
$$
\max_{\alpha, \beta: \alpha_i \ge 0} \theta_D(\alpha,\beta) 
$$
This optimization problem is called the *Lagrange dual problem*

Interpretation:
The dual problem searches for the penalty coefficients that produce the *best possible lower bound* on the optimal objective value.
Let
$$
d^* = \max_{\alpha, \beta: \alpha_i \ge 0} \theta_D (\alpha, \beta) $$
Then
$$
d^* \le p^* 
$$
This property is known as *weak duality*

### Why this is true

It follows from a simple inequality:
$$
\max \min \le \min \max
$$
This result is very general and holds for any function.

Another natural question arises:
*How tight can this bound be?*
Or saying, even we have the tightest bound, is it good enough for estimating the optimum point?
In general, the bound may be loose.
There can be a gap between the optimal values of the primal and dual problems:
$$
p^* - d^* > 0
$$
This difference is called the *duality gap*.
If such a gap exists, solving the dual problem could only gives an *approximation* of the true optimal value. So, can we rub off the gap? The answer is: Absolutely!

# 4. When the Bound Becomes Exact 

## Strong Duality

Surprisingly, for mang important optimization problems, the duality gap disappers.
In these cases we have:
$$
d^* = p^*
$$
This property is called *strong duality*.
When strong duality holds, solving the dual problem is *equivalent* to solving the primal problem.
So, when does *strong duality* hold?

It makes sense that strong duality does not hold for arbitrary optimization problems.
However, good news, a power result from convex optimization states that strong duality hold when:
- the primal problem is *convex*
- the problem satisfies *Slater's condition*

Slater's condition requires the existence of a strictly feasible point:
$$
g_i(w) < 0
$$
for all inequality constraints.

When this condition holds, the dual bound becomes exact.

## KKT Conditions

In last section, strong duality tells us that, under suitable conditions such as convexity and Slater's condition, the optimal values of the primal and dual problems coincide:
$$
d^* = p^*
$$
In this case, solving the dual problem is equivalent to solving the primal problem.
However, this raises a natural question:
*If the primal and dual optima coincide, how do we actually find the optimal solution?*
In other words, how can we recognize a pair of primal and dual variables that achieve this optimum.

The answer is given by a set of conditions known as *Karush-Kuhn-Tucker(KKT) conditions*.
These conditions describe the precise relationship between the optimal primal variable and the corresponding dual multipliers.

Intuitively, the KKT conditions combines three ideas:
- the primal variables must satisfy the original constraints
- the dual variables must respect the sigh restrcitions imposed by inequlity constraints
- thr primal and dual variables must interact in a way that balances the objective and the constraints

Theoretically, they are:
- Primal Feasibility
- Dual Feasibility
- Complementary Slackness

Formally, when strong duality holds and mild reqularity conditions are satisfied, 
a pair ($ w^*, \alpha^*, \beta^* $ is optimal if and only if it satisfies the *KKT conditions*.
These conditions therefore server as the *bridge between the primal and dual problems*,
providing a practical way to characterize and compute optimal solutions.

## Complementary Slackness

Among the KKT conditions, *complementary slackness* is the most conceptually interesting:
$$
\alpha_i g_i(w^*)$ = 0
$$
Instead of just stating it, explain its meaning:
- If a constraints is inactive ($g_i(w^*) < 0)$) -> its multiplier must be 0.
- If a multiplier is positive -> the constraints must be active.

So constraints behave like:
inactive constraint → no penalty
active constraint → positive penalty

This ties beautifully back to earlier explanation of *adaptive penalties*.

# 5. Example: Suppor Vector Machine
 
## How KKT is Used to Solve Problems

Next, we are going to explain that KKT conditions are *not just theory*,
they are actually used to solve optimization problems.

The typical workflow of it is:

1. Form Lagrangian
↓
2. Write KKT conditions
↓
3. Solve the resulting system

Let's go through it by an example about SVM.

Form Lagrangian

Suppose we are given a training dataset:
$$
(x_i, y_i),..., (x_n, y_n)
$$
where 
$$
y_i \in \{ -1, 1 \} 
$$
The goal of SVM is to find a hyperplane:
$$
w^T x + b = 0
$$
that seperates the two classes while maximizing the margin.

This leads to the following optimization problem:
$$
\min_{w,b} \frac{1}{2} \|w\|^2
$$
subject to the constraints
$$
y_i(w^T x_i + b) \ge 1, i = 1,..., n.
$$
The objective minimizes the norm of $ w $, which correponds to maximizing the margin,
while the constraints ensure that all points are correctly classified with a margin of at least 1.

Since the constraints are inequalities, we introduce non-negative Lagrange multipliers
$ \alpha_i \ge 0 $
The Lagrangian becomes
$$ 
L(w, \alpha, \beta) = \frac{1}{2} \|w\|^2 - \sum_{i=1}^n \alpha( y_i (w^T x_i + b)-1) 
$$
Here the multipliers $\alpha_i$ act as adaptive penalities: if a constraint is violated, 
the corresponding mulitiplier increases the objective value.

To  obtain the dual problem, we minimize the Lagrngian with respect to the primal variables $w, \beta$.

Setting the derivatives to zero give the *stationarity conditions*:
$$
\frac{\partial L}{\partial w} = w - \sum_{i=1}^n \alpha_i y_i x_i = 0
$$
which implies 
$$
w = \sum_{i=1}^n \alpha_i y_i x_i
$$

Disgress:
Among the KKT conditions, 
**stationarity** is the most fundamental and geometrically intuitive condition:
$$
\nabla L(w^*,\alpha^*) = 0
$$

Instead of just stating it, explain its meaning:
- At the optimal point $w^*$ the **gradient of the Lagrangian must vanish** — just like in unconstrained optimization.
- This means the gradient of the objective function is exactly balanced by a linear combination of the gradients of the active constraints:
  $$
  \nabla f(w^*) = \sum_i \alpha_i^* \nabla g_i(w^*)
  $$

So the optimality behaves like:
- Unconstrained optimum → gradient of objective is zero
- Constrained optimum → gradient of objective is **cancelled out** by constraint gradients

This ties beautifully back to the geometric intuition: 
at the optimum, you cannot move further downhill in the objective 
without violating at least one constraint.




Let's get back to main theme.

Similarly,
$$
\frac{\partial L}{\partial b} = -\sum_{i=1}^n \alpha_i y_i = 0
$$

Substituting these expressings back into the Lagrangian yields the *dual problem*
$$
\max_{\alpha} \sum_{i=1}^n \alpha_i - \frac{1}{2} \sum_{i,j} \alpha_i \alpha_j y_i y_j (x_i^T x_j)
$$
subject to 
$$
\alpha_i \ge 0,\quad \sum_{i=1}^n \alpha_i y_i = 0
$$

Write KKT conditons

The KKT conditions provide an important insight into the structure of the solution.
In particular, the complementary slackness condition requires
$$
\alpha_i (y_i (w^T x_i + b) - 1) = 0
$$
This means that for every training example, one of two things must happen:

If the constraint is not tight
$$
y_i (w^T x_i + b) > 1
$$
then
$$
\alpha_i = 0
$$

If the multiplier is positive
$$
\alpha_i > 0
$$
then the constraint must be active
$$
y_i (w^T x_i + b) = 1
$$
In other words, only the data points that lie exactly on the margin have nonzero multipliers.

These points are known as support vectors.
All other points have
$$\alpha_i = 0$$
and do not influence the final classifier.
This explains the name support vector machine: 
the optimal hyperplane is determined only by a small subset of the training data.

 Solving the Resulting System

Once the dual problem has been solved and the optimal multipliers $\alpha^*$ are obtained,
the KKT conditions allow us to recover the primal solution.

From the stationarity condition derived earlier, we have
$$
w = \sum_{i=1}^{n} \alpha_i y_i x_i.
$$
Thus the optimal weight vector is expressed as a *linear combination of the training points*.

However, due to the complementary slackness condition, most multipliers satisfy
$$
\alpha_i = 0.
$$
Only the *support vectors*, the points lying exactly on the margin, have nonzero multipliers. 
Therefore the weight vector can be written more compactly as
$$
w = \sum_{i \in SV} \alpha_i y_i x_i
$$
where $SV$ denotes the set of support vectors.

To determine the bias term $b$, 
we use the fact that *for any support vector the margin constraint is active*:
$$
y_i (w^T x_i + b) = 1.
$$
Rearranging gives
$$
b = y_i - w^T x_i.
$$
In practice, $b$ is often computed by averaging this expression over all support vectors.

Once $w$ and $b$ are known, the final classifier takes the form
$$
f(x) = \text{sign}(w^T x + b).
$$
Substituting the expression for $w$ yields
$$
f(x) = \text{sign}\left( \sum_{i \in SV} \alpha_i y_i (x_i^T x) + b \right).
$$
This formulation highlights an important property of SVMs: 
*the decision boundary depends only on the support vectors*, rather than the entire dataset.

Moreover, because the classifier depends only on inner products $x_i^T x$, 
these inner products can be replaced with kernel functions, 
enabling nonlinear decision boundaries through the kernel trick.

Connection to the Kernel Trick

The kernel function is shown as followed:
$$
K(x_i, x_j)
$$
By replacing inner products with kernel functions, 
the algorithm can operate in very high-dimensional feature spaces 
without explicitly computing the feature mapping.

This is the famous kernel trick, 
which allows SVMs to construct nonlinear decision boundaries.


What this Example Reveals

The SVM example illustrates several important ideas about 
Lagrange duality and constrained optimization.

First, the Lagrangian transforms a constrained optimization problem into a form 
where the constraints are incorporated into the objective through **Lagrange multipliers**. 
These multipliers act as adaptive penalties 
that adjust automatically depending on whether the constraints are satisfied.

Second, the **dual problem** often has a simpler structure than the primal problem. 
In the SVM case, the dual formulation depends only on inner products between data points. 
This not only simplifies the optimization but also enables the use of **kernel functions**, 
allowing the algorithm to operate in high-dimensional feature spaces 
without explicitly computing the feature mapping.

Third, the **KKT conditions** reveal the structural properties of the optimal solution. 
In particular, complementary slackness shows 
that only the constraints that are active at the optimum influence the final solution. 
For SVMs, this means that only the data points lying on the margin — the **support vectors** — 
determine the decision boundary.

This sparsity is one of the key reasons why SVMs are both efficient and interpretable.

---

# Conclusion

Lagrange duality provides a powerful framework 
for understanding and solving constrained optimization problems. 
By introducing Lagrange multipliers, 
a constrained problem can be transformed into a saddle-point problem 
involving both the primal variables and the multipliers.

From this formulation, the **dual problem** naturally emerges, 
providing a lower bound on the optimal objective value. 
Weak duality guarantees the existence of this bound, 
while strong duality ensures that under suitable conditions
—such as convexity and Slater’s condition—
the primal and dual optimal values coincide.

When strong duality holds, 
the **Karush–Kuhn–Tucker (KKT) conditions** characterize the optimal solutions. 
These conditions combine feasibility, optimality, and complementary slackness into 
a unified description of constrained optimization.

The example of support vector machines demonstrates how these ideas appear in practice.
Through the dual formulation, the optimization problem becomes easier to solve, 
the structure of the optimal solution becomes clearer, 
and powerful techniques such as the kernel trick become possible.

Taken together, Lagrange duality, strong duality, and the KKT conditions 
form a coherent theoretical framework 
that underlies many modern optimization algorithms in machine learning and beyond.



