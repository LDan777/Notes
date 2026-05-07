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

  基于锥的定义：广义不等式是利用 正常锥 (Proper Cone) K 来定义的 。  

  数学表示：对于两个向量 x,y，广义不等式  $x \le_K y$ 等价于 $y - x \in K$ 。  
    
  约束形式：在一个优化问题中，广义不等式约束通常写作 $g_i(x) \le_{K_i} 0$ ，这意味着函数值 $g_i(x)$ 必须落在锥 $-K_i$ 中 。
  </p>
</details>

---

## 3. 锥线性规划 (Conic Linear Programming, CLP)

CLP 是线性规划（LP）的直接推广，将非负约束 $x \ge 0$ 替换为锥约束 $x \ge_K 0$ 。

### 3.1 问题定义
锥线性规划的标准型通常可以表述为：

$$\begin{aligned}
\inf \quad & c \bullet x \\
\text{s.t.} \quad & a_i \bullet x = b_i, \quad i=1,\dots,m \\
& x \ge_K 0
\end{aligned}$$

- 这里的 ∙ 代表内积运算 。  
- $x \ge_K 0$ 表示向量 x 必须属于锥 K 。

### 3.2 常见的 CLP 形式
根据所选取的锥 K 不同，CLP 有几种极其重要的特殊形式：
|锥类型 K|对应的优化问题名称|备注
| :------- | :---------- | :----------------------------- |
|非负象限 ( $\mathbb{R}_+^n$ ​)|线性规划 (LP)|最基础的形式，所有分量非负 。|
|二阶锥/洛伦兹锥 ( $Q$ )|二阶锥规划 (SOCP)|约束形式通常为  $\|Ax + b\|_2 \le u^T x +d$ 。|
|正定锥 ( $S_+^n$ ​)|半正定规划 (SDP)|变量是矩阵 X，要求 X 是半正定矩阵 。|

### 3.3 对偶性质

锥线性规划也具有对称的对偶问题形式 ：

$$\begin{aligned}
\sup \quad & b^T y \\
\text{s.t.} \quad & \sum_{i=1}^{m} y_i a_i + s = c \\
& y \in \mathbb{R}^m, \quad s \ge_{K^*} 0
\end{aligned}$$

- **对偶变量**： $s$ 必须属于原锥 $K$ 的对偶锥 $K^*$  。 

- **弱对偶性**：对于任何原问题可行解 $x$ 和对偶问题可行解 (y,s)，总有 $b^T y \le c \bullet x$ 。  

- **强对偶性**：与 LP 不同，CLP 的强对偶性通常需要严格可行性（Strict Feasibility，即满足 Slater 条件）来保证 $p^* = d^*$  。
  
### 3.4 二次规划的 SDP 松弛 (SDR)

针对二次约束二次规划 (QCQP)，可以通过引入矩阵变量 $X = xx^T$ 将其转化为半正定规划 (SDP)。

* **松弛方法**：丢弃 $\text{rank}(X)=1$ 的非凸约束，仅保留 $X \ge 0$
* **紧致性 (Tightness)**：
  * 齐次情况下，若约束个数 $m \le 2$，松弛通常是紧的
    * **秩与约束个数的关系**：理论证明，如果松弛后的 SDP 问题有最优解，那么一定存在一个最优解 $X^*$ ，其秩 r 满足不等式  $\frac{r(r+1)}{2} \le m$ ，其中 m 是约束条件的个数 。
  * 非齐次情况下，依据 S-lemma，若 $m \le 1$ 且满足 Slater 条件，则松弛是紧的
    * **S-引理 (S-lemma)**：在非齐次二次规划中，如果满足以下两个条件，SDR 也是紧的 ：
      1. 约束个数 m≤1 。
      2. 满足 Slater 条件（即存在一个严格可行解） 。

* S-引理 (S-lemma)：

  **数学定义**：

   假设有两个二次函数 $f_0(x)$ 和 $f_1(x)$ 。S-lemma 探讨的是如下命题的等价性：

  命题 A：只要 $f_1(x) \le 0$，就一定有 $f_0(x) \le 0$ 。

  命题 B：存在一个非负常数 $\lambda \ge 0$，使得对于所有的 $x$，都有 $f_0(x) \le \lambda f_1(x)$ 。

  在矩阵形式下，这通常表示为：如果 $f_i(x) = x^T A_i x + 2b_i^T x + c_i$，则上述命题等价于存在 $\lambda \ge 0$ 满足如下 线性矩阵不等式 (LMI) ：

  <img width="624" height="204" alt="image" src="https://github.com/user-attachments/assets/4d8b4636-4fd5-45c7-8ae9-c1bfa74d4d0b" />


### 3.5 最大割问题 (Max-Cut Problem)

最大割问题可以等价写成二次优化形式，其 SDP 松弛是目前最经典的算法应用之一。

#### 3.5.1 原问题：Max-Cut 问题
##### 问题定义
$$
\begin{aligned}
\max_{x \in \mathbb{R}^n} \quad & x^T C x \\
\text{s.t.} \quad & x_i^2 = 1, \quad i=1,2,\dots,n
\end{aligned}
$$
- 变量 $x_i \in \{\pm1\}$ ，代表图中顶点的二分划分（ $+1$ 和 $-1$ 分属两个集合）。
- 矩阵 $C$ 通常为图的加权邻接矩阵， $x^T C x$ 是割边权重和的线性变换形式。



#### 3.5.2 第一步：推导 Max-Cut 的对偶问题
##### 1. 拉格朗日函数
$$
L(x,y) = -x^T C x + \sum_{i=1}^n y_i(x_i^2-1) = x^T(\text{Diag}(y)-C)x - \mathbf{1}^T y
$$
- $y_i$ 是约束 $x_i^2=1$ 对应的拉格朗日乘子。
- 为了将最大化问题转为标准对偶形式，这里对目标函数取了负号。

##### 2. 对偶函数
$$
g(y) = \inf_x L(x,y) =
\begin{cases}
-\mathbf{1}^T y, & \text{Diag}(y)-C \succeq 0 \\
-\infty, & \text{otherwise}
\end{cases}
$$
- 当 $\text{Diag}(y)-C \succeq 0$（半正定）时，二次型 $x^T(\text{Diag}(y)-C)x$ 的最小值为 $0$（取 $x=0$ 时），因此对偶函数值为 $-\mathbf{1}^T y$ 。
- 若矩阵不定，可令 $x$ 沿负特征值方向趋于无穷，使 $L(x,y) \to -\infty$ 。

##### 3. 对偶问题
$$
\begin{aligned}
\min_{y \in \mathbb{R}^n} \quad & \mathbf{1}^T y \\
\text{s.t.} \quad & \text{Diag}(y)-C \succeq 0
\end{aligned}
$$
- 这是一个**半定规划（SDP）问题**，也是 Max-Cut 的线性松弛对偶。


#### 3.5.3第二步：对偶问题的对偶（Max-Cut 的 SDP 松弛）
##### 1. 对偶问题的拉格朗日函数
$$
L(y,X) = \mathbf{1}^T y - \langle \text{Diag}(y)-C, X \rangle = \sum_{i=1}^n (1-X_{ii})y_i + \langle C, X \rangle
$$
- $X$ 是半定约束对应的拉格朗日乘子矩阵， $\langle A,B\rangle = \text{tr}(A^T B)$ 为矩阵内积。

##### 2. 对偶函数
$$
g(X) = \inf_y L(y,X) =
\begin{cases}
\langle C, X \rangle, & X_{ii}=1,\ i=1,\dots,n \\
-\infty, & \text{otherwise}
\end{cases}
$$
- 当 $X_{ii}=1$ 时， $y_i$ 的系数为 $0$ ，对偶函数值为常数 $\langle C, X \rangle$ 。
- 若 $X_{ii} \neq 1$，则可令对应的 $y_i$ 趋于无穷或负无穷，使 $L(y,X) \to -\infty$ 。

##### 3. 对偶问题（即 Max-Cut 的 SDP 松弛）
$$
\begin{aligned}
\max \quad & \langle C, X \rangle \\
\text{s.t.} \quad & X_{ii}=1, \quad i=1,2,\dots,n \\
& X \succeq 0
\end{aligned}
$$
- 这就是著名的**Max-Cut 半定松弛（SDR）**，去掉了原问题中 $X=xx^T$ 的秩-1约束，仅保留半正定和对角元为1的条件。
- 该松弛是 Max-Cut 问题最经典的近似算法，能保证得到最优值至少 $0.878$ 倍的近似解。

### 3.6 二阶锥规划（SOCP, Second-Order Cone Programming）



---

## 4. 鲁棒优化 (Robust Optimization)

当优化问题中的参数具有不确定性时，我们寻求在"最坏情况"下依然满足约束的解。

### 4.1 鲁棒对等式 (Robust Counterpart)

对于线性约束 $a_i^T x \le b_i$ ，若 $a_i$ 属于某个不确定性集合 $\mathcal{U}_i$ ：

* **多面体集**：鲁棒对等式仍为线性规划 (LP)
* **椭球集**：转化为二阶锥规划 (SOCP/CQP)
* **常见的转化表**：课堂总结了不同集合对应的转化难度

| 不确定集类型 | 定义 $\mathcal{Z}$ | 鲁棒等价形式 | 可处理类型 |
| :--- | :--- | :--- | :--- |
| **盒型（Box）** | $\|\zeta\|_\infty \le 1$ | $a^T x + \|P^T x\|_1 \le d$ | 线性规划（LP） |
| **椭球型（Ellipsoidal）** | $\|\zeta\|_2 \le 1$ | $a^T x + \|P^T x\|_2 \le d$ | 二次约束规划（CQP/SOCP） |
| **多面体（Polyhedral）** | $D\zeta + q \ge 0$ | $\begin{cases} a^T x + q^T w \le d \\ D^T w = -P^T x \\ w \ge 0 \end{cases}$ | 线性规划（LP） |
| **锥型（Cone）** | $D\zeta + q \in K$（ $K$ 为闭凸尖锥） | $\begin{cases} a^T x + q^T w \le d \\ D^T w = -P^T x \\ w \in K^* \end{cases}$ | 锥优化（Conic Opt.） |
| **凸约束（Convex cons.）** | $h_k(\zeta) \le 0,\ \forall k$ | $\begin{cases} a^T x + \sum_k u_k h_k^*(\frac{w^k}{u_k}) \le d \\ \sum_k w^k = P^T x \\ u \ge 0 \end{cases}$ | 凸优化（Convex Opt.） |

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
