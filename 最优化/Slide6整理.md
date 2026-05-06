# 锥规划（Conic Programming）课堂笔记

## 1. 基础理论：广义不等式与锥（Proper Cones）

### 1.1 正常锥（Proper Cone）的定义

一个集 $K \subseteq \mathbb{R}^n$ 被称为**正常锥**，必须满足以下四个几何特性：

1. **凸性 (Convex)**：对于任意 $x, y \in K$，有 $\alpha x + (1 - \alpha)y \in K$（其中 $0 \le \alpha \le 1$）。
2. **闭性 (Closed)**：包含其边界 $K = \text{cl } K$。
3. **有实腹 (Solid)**：内部非空 $\text{int } K \neq \emptyset$，即在空间中有体积。
4. **尖锐性 (Pointed)**：不包含任何直线（若 $x \in K$ 且 $-x \in K$，则 $x = 0$）。

### 1.2 广义不等式与偏序关系

借助正常锥 $K$ 建立向量偏序：

- **非严格偏序**：$x \ge_K y \iff x - y \in K$
- **严格偏序**：$x >_K y \iff x - y \in \text{int}(K)$（锥内部）

### 1.3 对偶锥（Dual Cone）

- **定义**：

$$K^* = \{ y \mid x^T y \ge 0,\ \forall x \in K \}$$

- **自对偶锥（Self-dual Cones）**：满足 $K = K^*$
  - 非负象限：$\mathbb{R}_+^n$
  - 洛伦兹锥（二阶锥 SOC）：$\mathcal{Q} = \{ (t, x) : t \ge \|x\|_2 \}$
  - 半正定锥（PSD Cone）：$\mathbb{S}_+^n = \{ X : X \succeq 0 \}$

---

## 2. 锥规划的标准形式与对偶（Conic Linear Programming）

### 2.1 广义拉格朗日函数

约束形式为 $g_i(x) \le_{K_i} 0$ 时：
- 拉格朗日乘子 $\lambda_i$ 不再是标量，而是属于**对偶锥 $K_i^*$** 的向量
- 对偶问题：$\max \theta(\lambda, \nu)$，约束 $\lambda_i \ge_{K_i^*} 0$

### 2.2 锥线性规划（CLP）对比（LP $\to$ CLP）

| 特性 | 线性规划（LP） | 锥线性规划（CLP） |
|------|----------------|--------------------|
| 原问题 | $\min c^T x \quad \text{s.t.}\ Ax = b,\ x \ge 0$ | $\inf c \cdot x \quad \text{s.t.}\ a_i \cdot x = b_i,\ x \ge_K 0$ |
| 对偶问题 | $\max b^T y \quad \text{s.t.}\ A^T y \le c$ | $\sup b^T y \quad \text{s.t.}\ \sum y_i a_i + s = c,\ s \ge_{K^*} 0$ |
| 变量约束 | 属于非负象限 | 属于广义凸锥 $K$ |

### 2.3 典型锥规划

- **二阶锥规划（SOCP）**：$K = \mathcal{Q}$，约束形如 $\|A_i x + b_i\| \le u_i^T x + v_i$
- **半正定规划（SDP）**：$K = \mathbb{S}_+^n$，内积 $A \cdot B = \text{trace}(A^T B)$

---

## 3. SDP 松弛与应用

### 3.1 QCQP 的半正定松弛（SDR）

非凸二次约束二次规划（QCQP）$\to$ SDP 步骤：

1. **变量替换**：$x^T A x = A \cdot xx^T$，引入矩阵变量 $X = xx^T$
2. **等价条件**：$X = xx^T \iff X \succeq xx^T$ 且 $\text{rank}(X) = 1$
3. **凸松弛**：丢弃秩约束 $\text{rank}(X) = 1$，得到凸 SDP
4. **紧致性**：特定条件下松弛精确（如齐次 $m \le 2$、Slater 条件且非齐次 $m \le 1$）

### 3.2 典型案例：Max-Cut 问题

- **目标**：最大化割边权重和，变量 $x_i \in \{1, -1\}$
- **松弛形式**：

$$\max \langle C, X \rangle, \quad \text{s.t.}\ X_{ii} = 1,\ X \succeq 0$$

---

## 4. 鲁棒优化（Robust Optimization）

### 4.1 鲁棒线性规划（RLP）

约束 $a_i^T x \le b_i$ 中参数 $a_i$ 不确定（属于集合 $U_i$），需对**所有可能参数**满足约束。

- 多面体不确定性：$U_i = \{ a_i : D_i a_i \le e_i \}$，由 LP 强对偶转化为普通 LP

### 4.2 不确定性集与鲁棒对应形式

| 不确定性集类型 | 集合描述 $Z$ | 鲁棒对应形式 | 可处理性 |
|----------------|------------------|--------------|----------|
| 盒子（Box） | $\|\zeta\|_\infty \le \cdot$ | $a^T x + \|P^T x\|_1 \le \cdot$ | LP |
| 椭球（Ellipsoidal） | $\|\zeta\|_2 \le \cdot$ | $a^T x + \|P^T x\|_2 \le \cdot$ | CQP（SOCP） |
| 多面体（Polyhedral） | $D\zeta + q \ge 0$ | 对偶线性系统 | LP |

---

## 5. 机会约束的近似（Chance Constraints）

### 5.1 问题描述

随机环境下允许约束以概率 $\delta$ 违反：

$$\text{Pr}(a^T x > b) \le \delta$$

### 5.2 安全可处理近似

直接求解概率约束为 NP-hard，采用**凸安全近似**：

- **Hoeffding 不等式**（独立同分布、扰动在 $[-1, 1]$）：

$$-y_0 \ge \sqrt{2\ln(1/\delta) \sum y_i^2}$$

- **结论**：上述二阶锥（SOC）约束是充分条件，可高效求解。
