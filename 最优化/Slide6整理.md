# 第 6 讲：锥优化 (Conic Programming) 与算法基础

<sub style="font-size: 0.5em;">

* [1. 广义不等式与锥](#1-广义不等式与锥-generalized-inequalities--cones)

  
* [2. 对偶锥与对偶问题](#2-对偶锥与对偶问题-dual-cones--duality)

  
* [3. 锥线性规划](#3-锥线性规划-conic-linear-programming-clp)

  
* [4. 鲁棒优化](#4-鲁棒优化-robust-optimization)

  
* [5. 梯度法基础](#5-梯度法基础-gradient-methods)

</sub>

## 1. 广义不等式与锥 (Generalized Inequalities & Cones)

### 1.1 正常锥 (Proper Cone) 的定义

一个锥 $K$ 如果满足以下四个条件，则被称为**正常锥**：

* **凸性 (Convex)**：对于 $\forall x, y \in K$ ，有 $\alpha x + (1-\alpha)y \in K$ 。
* **闭性 (Closed)**：包含其边界。
* **实心性 (Solid)**：内部非空（即具有内点）。
* **指向性 (Pointed)**：不包含直线。即若 $x \in K$ 且 $-x \in K$ ，则必有 $x=0$ 。

### 1.2 广义偏序关系

利用正常锥 $K$，我们可以定义向量间的偏序关系：

* **偏序**： $x \ge_K y \iff x - y \in K$
* **严格偏序**： $x >_K y \iff x - y \in \text{int}(K)$

**性质**：这些偏序关系在加法、非负数乘、传递性以及极限操作下保持不变。

---

## 2. 对偶锥与对偶问题 (Dual Cones & Duality)

### 2.1 对偶锥 (Dual Cone)

对偶锥 $K^*$ 的定义为：

$$K^* = \lbrace y \mid x^T y \ge 0, \forall x \in K\rbrace $$

如果 $K$ 是正常锥，则 $K^*$ 也是正常锥。

**自对偶锥 (Self-dual Cones) 示例**：

1. **非负象限 (Non-negative Orthant)**： $\mathbb{R}_+^n$
2. **洛伦兹锥 (Lorentz/Second-Order Cone)**： $\mathcal{Q} = \lbrace (t, x) : t \ge \|x\|_2 \rbrace$
3. **正定锥 (Positive Semidefinite Cone)**： $S_+^n$

### 2.2 广义不等式的拉格朗日对偶

对于包含广义不等式约束的问题，其拉格朗日函数的构造与标量情况类似，但乘子 $\lambda_i$ 需属于对偶锥 $K_i^*$ 。

* **原问题**：约束为 $g_i(x) \le_{K_i} 0$
* **对偶约束**：要求对偶变量 $\lambda_i \ge_{K_i^*} 0$
* **性质**：弱对偶性总是成立；在满足 Slater 条件下，强对偶性通常对凸问题成立。
  
<details>
  <summary style="color: #007BFF; cursor: pointer; font-weight: bold;">
    什么是广义不等式约束？ (点击查看备注)
  </summary>
  <p style="background-color: #f8f9fa; padding: 12px; border-left: 6px solid #007BFF; border-radius: 4px;">
    <b>备注：</b> 它是传统不等式的推广。不再是简单的“数”与“数”比大小，而是要求向量之差落在一个特定的“锥”空间内。
    例如在 SDP 中，它要求矩阵必须是半正定的。
  </p>
</details>

---

## 3. 锥线性规划 (Conic Linear Programming, CLP)

CLP 是线性规划（LP）的直接推广，将非负约束 $x \ge 0$ 替换为锥约束 $x \ge_K 0$ 。

### 3.1 二次规划的 SDP 松弛 (SDR)

针对二次约束二次规划 (QCQP)，可以通过引入矩阵变量 $X = xx^T$ 将其转化为半正定规划 (SDP)。

* **松弛方法**：丢弃 $\text{rank}(X)=1$ 的非凸约束，仅保留 $X \ge 0$
* **紧致性 (Tightness)**：
  * 齐次情况下，若约束个数 $m \le 2$，松弛通常是紧的
  * 非齐次情况下，依据 S-lemma，若 $m \le 1$ 且满足 Slater 条件，则松弛是紧的

### 3.2 最大割问题 (Max-Cut Problem)

最大割问题可以等价写成二次优化形式，其 SDP 松弛是目前最经典的算法应用之一。

---

## 4. 鲁棒优化 (Robust Optimization)

当优化问题中的参数具有不确定性时，我们寻求在"最坏情况"下依然满足约束的解。

### 4.1 鲁棒对等式 (Robust Counterpart)

对于线性约束 $a_i^T x \le b_i$ ，若 $a_i$ 属于某个不确定性集合 $\mathcal{U}_i$ ：

* **多面体集**：鲁棒对等式仍为线性规划 (LP)
* **椭球集**：转化为二阶锥规划 (SOCP/CQP)
* **常见的转化表**：课堂总结了不同集合对应的转化难度

### 4.2 机会约束 (Chance Constraints) 的安全近似

当要求约束以一定概率 $\delta$ 成立时（如 $\Pr(a^T x > b) \le \delta$ ），直接求解非常困难。

* **Hoeffding 不等式**：利用该不等式，可以将机会约束近似为一个更保守但可处理的**二阶锥约束**
* 这种近似被称为"安全可处理近似 (Safe Tractable Approximation)"

---

## 5. 梯度法基础 (Gradient Methods)

从本节开始进入数值算法部分，重点讨论无约束优化问题的迭代求解。

### 5.1 算法框架

迭代格式： $x_{k+1} = x_k + t_k p_k$

* $p_k$：下降方向，梯度法取负梯度方向 $-\nabla f(x_k)$
* $t_k$：步长 (Step size)

### 5.2 步长选择策略

1. **恒定步长**： $t_k = \alpha$
2. **精确线搜索 (Exact Line Search)**：寻找使沿方向函数值最小的 $t$
3. **回溯线搜索 (Backtracking Line Search)**：最实用。从一个大步长开始，如果不满足下降准则（如 Armijo 条件），则不断缩减步长（乘上 $\beta < 1$ ）

### 5.3 停止准则 (Stopping Criteria)

由于无法达到绝对的 $\nabla f(x) = 0$ ，通常使用以下准则：

* 梯度范数小于极小值： $\|\nabla f(x_k)\| \le \epsilon$
* 函数值变化量或自变量变化量足够小

### 5.4 收敛性分析：Lipschitz 连续梯度

* **$L$-光滑假设**：假设梯度满足 Lipschitz 连续条件 $\|\nabla f(x) - \nabla f(y)\| \le L\|x-y\|$
* **二次上界性质**：若梯度 $L$ -连续，则函数具有二次上界：

$$f(y) \le f(x) + \nabla f(x)^T(y-x) + \frac{L}{2}\|y-x\|^2$$

* **充分下降引理 (Sufficient Decrease Lemma)**：利用上述上界可以证明，只要步长选取得当（如 $t < 2/L$ ），每一步迭代函数值都会有确定的下降量。

---

### **知识补充：初学者注意点**

* **什么是条件数 (Condition Number)？**

老师在 02:03:39 提到，梯度法的收敛速度受条件数影响。简单来说，如果函数等值线像一个瘦长的橄榄球（条件数大），梯度方向会偏离圆心，导致算法在两侧不断"振荡"（锯齿现象），收敛极慢。

* **SDP 与 LP 的区别**：

LP 处理的是向量，其非负约束是元素级的；SDP 处理的是矩阵，其正定约束是整体性质的。
