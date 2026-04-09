# 凸优化 第3讲 完整讲义（零基础易懂版）

---

## 一、优化问题的标准形式（最基础框架）
所有优化问题都可以写成统一的**标准形式**，这是我们学习的起点。

### 1. 标准形式公式
\[
\begin{align*}
\min \quad & f_0(x) \\
\text{s.t.} \quad & f_i(x) \leq 0, \quad i=1,\dots,m \\
& h_j(x) = 0, \quad j=1,\dots,p
\end{align*}
\]
符号解释：
- \(\min f_0(x)\)：**目标函数**，我们要最小化的函数；
- \(\text{s.t.}\)：subject to，意为“受限于”；
- \(f_i(x) \leq 0\)：**不等式约束**，共 \(m\) 个；
- \(h_j(x) = 0\)：**等式约束**，共 \(p\) 个。

### 2. 三个关键定义
1. **可行点（Feasible x）**
满足所有约束，并且在目标函数定义域内的点 \(x\)，就是可行点。
2. **最优解（Optimal \(x^*\)）**
对**所有**可行点 \(x\)，都有 \(f_0(x^*) \leq f_0(x)\)，这个 \(x^*\) 就是全局最优解。
3. **局部最优解（Locally optimal \(x^*\)）**
只在 \(x^*\) 附近一个小范围（半径 \(R>0\) 的圆/球内）最优，不保证全局最好。

---

## 二、凸优化问题（核心定义）
凸优化是整个课程最重要的一类问题，**定义非常严格**。

### 1. 凸优化的严格定义
一个优化问题是**凸优化问题**，必须同时满足3个条件：
1. 目标函数 \(f_0(x)\) 是**凸函数**；
2. 所有不等式约束 \(f_i(x)\) 都是**凸函数**；
3. 所有等式约束 \(h_j(x)\) 都是**仿射函数**（形如 \(Ax+b=0\)）。

### 2. 凸优化最重要的性质
**凸优化问题的任何局部最优解，都是全局最优解。**
- 通俗理解：只要找到一个“局部最低点”，它就一定是**整个问题的最低点**；
- 这是非凸问题没有的巨大优势，也是凸优化好用的根本原因。

---

## 三、常见凸优化问题（从简单到复杂）
我们按**难度从低到高**，把PPT里的所有问题串起来讲。

### 1. 线性规划（LP）——最简单的凸优化
#### （1）标准形式
\[
\begin{align*}
\min \quad & c^T x \\
\text{s.t.} \quad & Ax = b \\
& x \geq 0
\end{align*}
\]
特点：目标和约束全是**线性函数**，一定满足凸优化定义。

#### （2）一般线性规划转标准形式
任意线性规划都能写成上面的标准形式：
\[
\begin{align*}
\min \quad & c^T x + d \\
\text{s.t.} \quad & Gx \leq h \\
& Ax = b
\end{align*}
\]
常数 \(d\) 不影响最优解，可直接忽略。

#### （3）经典例子：饮食问题
- 目标：买 \(n\) 种食物，总花费最少；
- 约束：每种营养摄入量≥最低要求，食物数量≥0；
- 模型：
\[
\begin{align*}
\min \quad & c^T x \\
\text{s.t.} \quad & Ax \geq b \\
& x \geq 0
\end{align*}
\]

#### （4）分段线性最小化
目标：\(\min \max_{i=1,\dots,m}(a_i^T x + b_i)\)
等价转化为线性规划：
\[
\begin{align*}
\min \quad & t \\
\text{s.t.} \quad & a_i^T x + b_i \leq t, \quad \forall i
\end{align*}
\]
用新变量 \(t\) 代替最大值，把非光滑问题变光滑。

### 2. 线性分式规划
目标函数是**两个线性函数的比值**：
\[
f_0(x) = \frac{c^T x + d}{e^T x + f}, \quad \text{定义域} \ e^T x + f > 0
\]
**可以等价转化为线性规划**（变量 \(y,z\)）：
\[
\begin{align*}
\min \quad & c^T y + d z \\
\text{s.t.} \quad & Gy \leq h z,\ Ay = b z \\
& e^T y + f z = 1,\ z \geq 0
\end{align*}
\]
转化后就能用LP求解器计算。

### 3. 二次规划（QP）
#### （1）标准形式
\[
\begin{align*}
\min \quad & \frac{1}{2}x^T P x + q^T x + r \\
\text{s.t.} \quad & Gx \leq h \\
& Ax = b
\end{align*}
\]
要求：\(P\) 是**半正定矩阵**（\(P \succeq 0\)），保证目标函数是凸二次函数。

#### （2）经典例子：最小二乘
\[
\min \|Ax - b\|_2^2
\]
解析解：\(x^* = (A^T A)^{-1}A^T b\)（伪逆），可加线性约束。

#### （3）应用：马科维茨投资组合优化
- 目标：最小化投资组合风险（方差 \(x^T \Sigma x\)）；
- 约束：收益≥最低要求、总资金=1、不允许卖空（\(x≥0\)）；
- 模型：
\[
\begin{align*}
\min \quad & x^T \Sigma x \\
\text{s.t.} \quad & \bar{p}^T x \geq r_{\text{min}} \\
& e^T x = 1,\ x \geq 0
\end{align*}
\]

### 4. 支持向量机（SVM）——凸优化经典应用
#### （1）线性可分情况
目标：找到一个超平面 \(w^T x + \beta = 0\)，把两类点完全分开。
凸优化模型：
\[
\begin{align*}
\min \quad & \frac{1}{2}\|w\|_2^2 \\
\text{s.t.} \quad & b_i(w^T x_i + \beta) \geq 1, \quad \forall i
\end{align*}
\]
这是一个**二次规划**，一定有全局最优。

#### （2）线性不可分情况
引入松弛变量 \(\zeta_i\)，允许少量点分错：
\[
\min \frac{1}{2}\|w\|_2^2 + \mu \sum \max\{1 - b_i(w^T x_i + \beta), 0\}
\]
稀疏版本用L1正则：\(\min \|w\|_1 + \dots\)。

### 5. 二次约束二次规划（QCQP）
目标和约束**都是凸二次函数**：
\[
\begin{align*}
\min \quad & \frac{1}{2}x^T P_0 x + q_0^T x + r_0 \\
\text{s.t.} \quad & \frac{1}{2}x^T P_i x + q_i^T x + r_i \leq 0, \quad i=1,\dots,m
\end{align*}
\]
所有 \(P_i \succeq 0\)，可行域是椭球交集，仍是凸集。

### 6. 二阶锥规划（SOCP）
比QCQP更通用，约束是**二阶锥形式**：
\[
\begin{align*}
\min \quad & f^T x \\
\text{s.t.} \quad & \|A_i^T x + b_i\|_2 \leq c_i^T x + d_i \\
& Fx = g
\end{align*}
\]
- 特殊情况：\(n_i=0\) 退化为LP；\(c_i=0\) 退化为QCQP；
- 是处理鲁棒优化的核心工具。

---

## 四、鲁棒线性规划（带不确定性的优化）
实际问题中参数（\(c,a_i,b_i\)）往往不确定，有两种处理思路。

### 1. 确定性鲁棒方法
约束对**所有**不确定参数都成立：
\[
\min c^T x,\quad \text{s.t.} \ a_i^T x \leq b_i,\ \forall a_i \in \mathcal{E}_i
\]
若不确定集是椭球：
\[
\mathcal{E}_i = \{\bar{a}_i + P_i u \mid \|u\|_2 \leq 1\}
\]
等价于SOCP约束：
\[
\bar{a}_i^T x + \|P_i^T x\|_2 \leq b_i
\]

### 2. 随机鲁棒方法（机会约束）
约束以**足够大概率**成立：
\[
\mathbb{P}(a_i^T x \leq b_i) \geq \eta
\]
若 \(a_i\) 服从高斯分布，等价于SOCP：
\[
\bar{a}_i^T x + \Phi^{-1}(\eta)\|\Sigma_i^{1/2}x\|_2 \leq b_i
\]
\(\Phi\) 是标准正态分布累积函数。

---

## 五、优化求解工具与CVX使用
### 1. 常用求解器
- **MOSEK/Sedumi/SDPT3**：线性规划、锥规划；
- **Gurobi/Cplex**：整数规划、线性/二次规划；
- **Ipopt**：通用非线性规划（局部最优）；
- **BARON**：全局非线性规划。

### 2. CVX工具（Matlab凸优化建模）
CVX把Matlab变成凸优化语言，按规则写表达式即可自动识别凸性。

#### 基本结构
```matlab
cvx_begin
    variable x(n)           % 声明变量
    minimize(目标函数)      % 目标
    subject to              % 约束
        约束1;
        约束2;
cvx_end
```

#### 最小二乘例子
```matlab
cvx_begin
    variable x(n)
    minimize( norm(A*x - b, 2) )
    subject to
        norm(x, Inf) <= e;
        C*x == d;
cvx_end
```

#### 线性规划例子
```matlab
cvx_begin
    variable x(n)
    minimize(c'*x)
    subject to
        A*x == b;
        x >= 0;
cvx_end
```

---

## 六、规范凸规划（DCP）
### 1. 定义
由Michael Grant、Stephen Boyd等人提出，**一套写凸优化的规则**。

### 2. 核心作用
- 符合DCP规则的问题，工具**自动判定为凸**并求解；
- 不符合规则，即使问题本身凸，工具也会拒绝；
- 目的：保证建模规范、求解稳定。

---

## 全讲总结
1. 凸优化 = 凸目标 + 凸不等式约束 + 仿射等式约束；
2. 凸优化**局部最优=全局最优**；
3. 常见凸问题：LP → 线性分式 → QP → QCQP → SOCP（越来越通用）；
4. 鲁棒优化可转化为SOCP求解；
5. CVX按DCP规则建模，一行代码调求解器。

