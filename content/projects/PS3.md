---
title: "Problem 1"
draft: false
share: true
---
# Problem 1
$$
\frac{2}{m} \sum_{i=1}^{m} \underbrace{\left(o^{(i)} - y^{(i)}\right)}_{\text{误差}} \cdot \underbrace{o^{(i)}\left(1 - o^{(i)}\right)}_{\text{输出层导数}} \cdot \underbrace{w_2^{[2]}}_{\text{路径权重}} \cdot \underbrace{h_2^{(i)}\left(1 - h_2^{(i)}\right)}_{\text{隐藏层导数}} \cdot \underbrace{x_1^{(i)}}_{\text{输入}}
$$

路径权重为 0 时，使得链式法则中的某一项导数为 0，
从而使整个梯度乘积为 0，导致梯度无法沿该路径传播。
本质上依赖的是链式法则的乘法结构。


step function 网络可以构造**多边形决策边界**，而三角形正好由三个半平面交集形成。

---

# 🧠 几何本质

## 1️⃣ 单个隐藏神经元

$$  
h_i = \mathbf{1}(w_i^T x + b_i \ge 0)  
$$

几何意义：

- 决策边界：  
    $$  
    w_i^T x + b_i = 0  
    $$
    
- 对应半平面：  
    $$  
    {x \mid w_i^T x + b_i \ge 0}  
    $$
    

---

## 2️⃣ 三个隐藏神经元

三个半平面：

$$  
{x \mid w_1^T x + b_1 \ge 0}  
$$

$$  
{x \mid w_2^T x + b_2 \ge 0}  
$$

$$  
{x \mid w_3^T x + b_3 \ge 0}  
$$

取交集：

$$  
\bigcap_{i=1}^3 {x \mid w_i^T x + b_i \ge 0}  
$$

得到：

👉 一个三角形区域

---

# ⚙️ 输出层构造

隐藏层输出：

$$  
(h_1, h_2, h_3) \in \{0,1\}^3  
$$

输出层：

$$  
o = \mathbf{1}(w^T h + b \ge 0)  
$$

取：

$$  
w = (1,1,1)  
$$

$$  
o = \mathbf{1}(h_1 + h_2 + h_3 - \text{threshold} \ge 0)  
$$

---

## ✔ 阈值条件

为了满足：

- $$(1,1,1) \rightarrow 1$$
    
- 其他 $\rightarrow 0$
    

需要：

$$  
2 < \text{threshold} \le 3  
$$

---

