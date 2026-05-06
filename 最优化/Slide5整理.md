# 第 5 讲：拉格朗日对偶性 (Lagrangian Duality)

## 1. 原问题 (Primal Problem)

在优化理论中，我们首先定义一个标准形式的原问题 (P)，它**不需要一定是凸问题**：

$$\begin{aligned}
\min_{x} \quad & f(x) \\
\text{s.t.} \quad & g_i(x) \leq 0, \quad i = 1, \ldots, m \\
& h_j(x) = 0, \quad j = 1, \ldots, p \\
& x \in X
\end{aligned}$$

- **决策变量**： $x \in \mathbb{R}^n$
- **约束条件**： $g_i(x)$ 为不等式约束，$h_j(x)$ 为等式约束
- **乘子 (Multipliers)**： $\lambda_i$ 对应不等式约束（需满足 $\lambda_i \geq 0$），$\nu_j$ 对应等式约束
- **集合 $X$**：一个任意的非空子集，通常包含一些容易处理的约束

---

## 2. 拉格朗日函数与对偶函数

### 2.1 拉格朗日函数 (Lagrangian)

将目标函数与约束条件的线性组合定义为拉格朗日函数：

$$L(x, \lambda, \nu) = f(x) + \sum_{i=1}^{m} \lambda_i g_i(x) + \sum_{j=1}^{p} \nu_j h_j(x)$$

### 2.2 对偶函数 (Dual Function)

对偶函数 $\theta(\lambda, \nu)$ 是拉格朗日函数关于 $x$ 在集合 $X$ 上的下确界：

$$\theta(\lambda, \nu) = \inf_{x \in X} L(x, \lambda, \nu)$$

> **补充知识**：即使原问题是非凸的，对偶函数 $\theta(\lambda, \nu)$  **始终是一个凹函数**。这是因为它是关于 $(\lambda, \nu)$ 的一系列仿射函数的逐点下确界。

### 2.3 原问题的极小极大描述

原问题可以等价地写为：

$$\inf_{x \in X} \sup_{\lambda \geq 0, \nu} L(x, \lambda, \nu)$$

**推导逻辑**：如果 $x$ 不可行（即违反约束），内部的 $\sup$ 会趋向 $+\infty$；如果 $x$ 可行，内部 $\sup$ 的最大值恰好是 $f(x)$。

---

## 3. 对偶问题与弱对偶性

### 3.1 对偶问题 (Dual Problem)

对偶问题 (D) 是极大化对偶函数：

$$v_d^* = \sup_{\lambda \geq 0, \nu} \theta(\lambda, \nu)$$

### 3.2 弱对偶定理 (Weak Duality Theorem)

对于任何可行解，对偶问题的最优值总是小于或等于原问题的最优值：

$$\theta(\bar{\lambda}, \bar{\nu}) \leq f(\bar{x})$$

**含义**：对偶问题为原问题提供了一个**下界 (Lower Bound)**。

- **对偶间隔 (Duality Gap)**： $v_p^* - v_d^*$ 的值。如果大于 0，则存在对偶间隔。

---

## 4. 鞍点与强对偶性 (Saddle Points)

### 4.1 鞍点的定义

三元组 $(\bar{x}, \bar{\lambda}, \bar{\nu})$ 被称为**鞍点**，需满足：

- $\bar{x} \in X$ 且 $\bar{\lambda} \geq 0$
- 对于所有 $x, \lambda, \nu$ ：

$$L(\bar{x}, \lambda, \nu) \leq L(\bar{x}, \bar{\lambda}, \bar{\nu}) \leq L(x, \bar{\lambda}, \bar{\nu})$$

**即**：在给定乘子时， $\bar{x}$ 极小化 $L$ ；在给定 $\bar{x}$ 时，乘子极大化 $L$ 。

### 4.2 鞍点与强对偶的等价性

**定理**：存在鞍点 **当且仅当** 强对偶成立（对偶间隔为零），且该点分别是原问题和对偶问题的最优解。

### 4.3 鞍点最优条件 (与 KKT 类似)

一个点是鞍点的充要条件包括：

1. **原问题可行性**： $\bar{x} \in X, G(\bar{x}) \leq 0, H(\bar{x}) = 0$
2. **拉格朗日最优性**： $\bar{x} = \arg\min_{x \in X} L(x, \bar{\lambda}, \bar{\nu})$
3. **互补松弛性 (Complementarity)**： $\bar{\lambda}^T G(\bar{x}) = 0$

---

## 5. 凸问题下的强对偶性

在凸优化情况下，强对偶性更容易成立。

**前提条件**：
- $f, g_i$ 是**凸函数**
- $h_j$ 是**仿射函数**
- 满足 **Slater 条件**（存在一个点使得所有不等式约束严格成立，即 $g_i(x) < 0$ ）

**结论**：原问题最优值等于对偶问题最优值

$$v_p^* = v_d^*$$

---

## 6. 重要示例与应用

### 6.1 线性规划 (LP) 的对偶

**原问题**：

$$\min c^T x \quad \text{s.t.} \quad Ax = b, \quad x \geq 0$$

**对偶问题**：

$$\max -b^T \nu \quad \text{s.t.} \quad A^T \nu + c \geq 0$$

### 6.2 二次规划 (QP) 的对偶

对于 $\min x^T P x \quad \text{s.t.} \quad Ax \leq b$ ，其对偶形式通常涉及 $P^{-1}$ ：

$$\max -\frac{1}{4} \lambda^T A P^{-1} A^T \lambda - b^T \lambda \quad \text{s.t.} \quad \lambda \geq 0$$

### 6.3 二次约束二次规划 (QCQP) 的半定松弛 (SDP Relaxation)

针对非凸的 QCQP：

1. 利用恒等式 $x^T A x = \text{Tr}(A \cdot xx^T)$ 将变量升维
2. 令 $X = xx^T$ ，则约束变为 $X \succeq 0$ 且 $\text{rank}(X) = 1$
3. **松弛策略**：去掉非凸的"秩为 1"约束，得到一个凸的 SDP 问题

**意义**：SDP 松弛可以为复杂的非凸问题提供高质量的下界估计。

---

## 7. 进阶内容：广义不等式与锥规划 (Cone Programming)

### 7.1 正常锥 (Proper Cone)

用于定义广义不等式。满足：
- **凸** (Convex)
- **闭** (Closed)
- **实心**（有非空内部）(Solid)
- **尖**（不包含直线）(Pointed)

**常见锥**：
- 非负象限 $\mathbb{R}_+^n$
- 二阶锥 (SOC / Second-Order Cone)
- 半正定锥 (SDP Cone $\mathbb{S}_+^n$ )

### 7.2 对偶锥 (Dual Cone)

定义为

$$K^* = \{ y \mid x^T y \geq 0, \forall x \in K \}$$

**自对偶性**：上述三个常用锥均为自对偶锥，即 $K = K^*$ 。

### 7.3 锥规划的对偶

在广义不等式 $g_i(x) \leq_{K_i} 0$ 下，拉格朗日乘子 $\lambda_i$ 必须属于对应的对偶锥 $K_i^*$ 。**弱对偶定理**在广义不等式下依然成立。

---

## 复习提示

- **重点推导**：理解为什么原问题可以写成 $\inf\sup$ 而对偶是 $\sup\inf$
- **核心工具**：掌握如何利用共轭函数 (Fenchel Dual) 求解对偶问题
- **实践价值**：理解 SDP 松弛如何将难以处理的非凸二次约束转化为可计算的凸问题
