#  优化算法课堂笔记：牛顿法及其变体深度解析



## 一、 经典牛顿法（Pure Newton's Method）

### 1. 核心假设

假设目标函数 $f(x): \mathbb{R}^n \to \mathbb{R}$ 满足：

* **二次连续可微（Twice Continuously Differentiable）**：保证海森矩阵（Hessian Matrix） $\nabla^2 f(x)$ 存在且连续 。


* **凸函数（Convex）**：保证局部极小值即为全局最小值 。



### 2. 基本原理：局部二次近似

牛顿法的核心思想是在当前迭代点 $x_k$ 处，利用 **泰勒展开（Taylor Expansion）** 的前三项来构造一个二次函数来局部近似目标函数 $f(x)$ ：


$$f(x_k + p_k) \approx \hat{f}(p_k) = f(x_k) + \nabla f(x_k)^T p_k + \frac{1}{2} p_k^T \nabla^2 f(x_k) p_k$$

其中 $p_k$ 是从 $x_k$ 出发的寻优步长（向量） 。

### 3. 牛顿步（Newton Step）的推导

为了找到使局部近似函数 $\hat{f}(p_k)$ 最小化的最优步长 $p_k$ ，我们对 $\hat{f}(p_k)$ 关于 $p_k$ 求梯度，并令其等于 $0$ （一阶必要条件） ：


$$\nabla_{p_k} \hat{f}(p_k) = \nabla f(x_k) + \nabla^2 f(x_k) p_k = 0$$



当海森矩阵 $\nabla^2 f(x_k)$ **严格正定**（Strictly Positive Definite, $\nabla^2 f(x_k) \succ 0$ ）时，该矩阵可逆 。此时可唯一解出最优步长，即为 **牛顿方向/牛顿步（Newton Direction / Step）**，记为 $d(x_k)$ ：


$$d(x_k) = p_k = -\nabla^2 f(x_k)^{-1} \nabla f(x_k)$$



**下一次迭代点的更新公式** ：


$$x_{k+1} = x_k + d(x_k) = x_k - \nabla^2 f(x_k)^{-1} \nabla f(x_k)$$



---

## 二、 牛顿方向的重要数学性质

### 1. 下降方向性质（Descent Direction）

> 
> **定理**：若海森矩阵 $\nabla^2 f(x_k)$ 严格正定，且当前点梯度 $\nabla f(x_k) \neq 0$ ，则牛顿方向 $d(x_k)$ 必定是 $f(x)$ 在 $x_k$ 处的**严格下降方向** 。
> 
> 

* **证明与推导**：
要证 $d(x_k)$ 是下降方向，只需证明它与梯度的内积严格小于 $0$ ：



$$\langle \nabla f(x_k), d(x_k) \rangle = \nabla f(x_k)^T \left( -\nabla^2 f(x_k)^{-1} \nabla f(x_k) \right) = - \nabla f(x_k)^T \nabla^2 f(x_k)^{-1} \nabla f(x_k)$$



因为 $\nabla^2 f(x_k) \succ 0$ ，其逆矩阵同样严格正定，即对任意非零向量 $v$ ，必有 $v^T \nabla^2 f(x_k)^{-1} v > 0$ 。
令 $v = \nabla f(x_k) \neq 0$ ，则：



$$\nabla f(x_k)^T \nabla^2 f(x_k)^{-1} \nabla f(x_k) > 0 \implies \langle \nabla f(x_k), d(x_k) \rangle < 0 \quad \text{(证毕)}$$






### 2. 仿射不变性（Affine Invariance）

牛顿步具有一个极佳的几何特性：**其对坐标系的线性变换（或仿射变换）不敏感** 。这意味着对问题进行尺度缩放（Scaling）不会影响牛顿法的收敛轨迹，而梯度下降法则极度依赖坐标缩放 。

### 3. 牛顿减量（Newton's Decrement）

定义 $\lambda(x)$ 为牛顿减量 ：


$$\lambda(x) = \left( \nabla f(x)^T \nabla^2 f(x)^{-1} \nabla f(x) \right)^{1/2}$$



* **数学关系**： $\lambda(x)^2 = -\nabla f(x)^T d(x)$ 。


* **物理意义**： $\lambda(x)^2/2$ 近似描述了当前点函数值 $f(x)$ 与局部极小值之间的差距（即停止准则的依据） 。



---

## 三、 纯牛顿法的缺陷：为什么会发散？

虽然纯牛顿法收敛极快，但它**缺乏全局收敛性**。如果初始点 $x_0$ 离极小值点太远，算法可能会剧烈发散 。

### 经典发散案例分析

考虑无约束优化问题： $\min \, f(x) = \sqrt{1+x^2}$ ，其唯一全局最优解为 $x^* = 0$ 。

* **一阶与二阶导数计算** ：



$$f'(x) = \frac{x}{\sqrt{1+x^2}}, \quad f''(x) = \frac{1}{(1+x^2)^{3/2}}$$





* **牛顿迭代步推导** ：



$$x_{k+1} = x_k - \frac{f'(x_k)}{f''(x_k)} = x_k - \frac{x_k / \sqrt{1+x_k^2}}{1 / (1+x_k^2)^{3/2}} = x_k - x_k(1+x_k^2) = -x_k^3$$





* **收敛性行为判定** ：


* 若 $|x_0| [cite_start]< 1$ ，由于每次迭代立方后绝对值变小，算法最终**收敛**到 $0$ 。


* 若 $|x_0| = 1$ ，则 $x_1 = -x_0$ ，算法将在 $-1$ 和 $1$ 之间无限交替震荡。
* 若 $|x_0| \ge 1$ ，则 $|x_{k+1}| [cite_start]= |x_k|^3 > |x_k|$ ，迭代值呈爆炸式**发散** 。





---

## 四、 阻尼牛顿法（Damped Newton's Method）

为了解决纯牛顿法容易发散的问题，引入**步长参数 $t_k$**，通过回溯线搜索（Backtracking Line Search）来确保每次迭代函数值都严格下降 。

算法 1：阻尼牛顿法完整流程 

* 
**输入**：初始点 $x_0$, 回溯参数 $\alpha \in (0, 0.5), \beta \in (0, 1)$, 容忍度 $\epsilon > 0$ 。


* 
**循环迭代** ($k = 0, 1, 2, \dots$) ：


1. 计算牛顿方向 $d_k = -\nabla^2 f(x_k)^{-1} \nabla f(x_k)$ 。


2. 计算牛顿减量平方 $\lambda^2 = \nabla f(x_k)^T \nabla^2 f(x_k)^{-1} \nabla f(x_k)$ 。


3. **终止检验**：若 $\frac{\lambda^2}{2} \le \epsilon$ ，则停止迭代，输出 $x_k$ 。


4. **线搜索初始化**：置步长 $t_k = 1$ 。


5. **回溯寻找步长（Armijo 条件）**：

$$\text{while } f(x_k + t_k d_k) > f(x_k) - \alpha t_k \lambda^2 \quad \text{do}$$



$$\quad t_k = \beta t_k$$





6. **更新状态**： $x_{k+1} = x_k + t_k d_k$ 。





---

## 五、 阻尼牛顿法的收敛性理论（强凸与Lipschitz海森）

### 1. 标准数学假设

设 $f$ 在 $\mathbb{R}^n$ 上二次连续可微，且满足 ：

1. **海森矩阵上下界（强凸与平滑性）**：存在常数 $M > m > 0$ ，使得 ：



$$mI \preceq \nabla^2 f(x) \preceq MI \quad (\forall x \in \mathbb{R}^n)$$





2. **海森矩阵的 Lipschitz 连续性**：存在 $L > 0$ ，使得 ：



$$\|\nabla^2 f(x) - \nabla^2 f(y)\|_2 \le L \|x - y\|_2 \quad (\forall x, y \in \mathbb{R}^n)$$






### 2. 两阶段收敛行为 (Two-Phase Convergence)

在该假设下，阻尼牛顿法的收敛过程会清晰地划分为**两个阶段** ：存在一个临界阀值 $\eta$ （满足 $0 < \eta \le \frac{m^2}{L}$ ）和常数 $\gamma > 0$ ：

#### 阶段 1：阻尼牛顿阶段（Sublinear 阶段）

* **触发条件**：当当前梯度较大，即 $\|\nabla f(x_k)\| [cite_start]\ge \eta$ 时 。


* **核心结论**：回溯线搜索能够保证步长 $t_k < 1$ ，且每次迭代目标函数值至少下降一个固定的正数 $\gamma$ ：



$$f(x_{k+1}) - f(x_k) \le -\gamma$$





* **因果意义**：由于目标函数有下界（强凸），该阶段必然在有限步内结束，确保了**全局收敛性**。

#### 阶段 2：纯牛顿阶段（Quadratic 阶段）

* **触发条件**：一旦梯度落入小区域，即 $\|\nabla f(x_k)\| [cite_start]< \eta$ 。


* **核心结论**：此时回溯线搜索条件会永远被满足，使得**步长恒等于 $1$** ($t_k \equiv 1$) 。算法自动转为纯牛顿法，且梯度满足下述不等式 ：



$$\frac{L}{2m^2}\|\nabla f(x_{k+1})\| [cite_start]\le \left( \frac{L}{2m^2}\|\nabla f(x_k)\| \right)^2$$


由此可导出其著名的**二次收敛速度（Quadratic Convergence）** ：



$$\frac{L}{2m^2}\|\nabla f(x_{k+1})\| [cite_start]\le \left(\frac{1}{2}\right)^{2^{l-k}}$$





* **快速收敛的实例证明**：在实际应用中（例如 $R^{10000}$ 的大规模优化问题），牛顿法由于这种二次收敛特性，往往只需少少十几步（如 18 步）即可达到极高的精度 。



---

## 六、 算法工程实现与大规模扩展：不精确牛顿法（Inexact Newton）

### 1. 经典牛顿法的计算瓶颈

在每一步迭代中，计算的核心开销来自于求解线性方程组（牛顿系统） ：


$$\nabla^2 f(x_k) d_k = -\nabla f(x_k)$$



* **Cholesky 分解法**：对海森矩阵进行分解 $\nabla^2 f(x_k) = LL^T$ 。对于稠密矩阵，其计算复杂度高达 $O(\frac{1}{3}n^3)$ Flops，空间复杂度为 $O(n^2)$ 。


* **大规模瓶颈**：当维度 $n$ 极大时（如 $n > 10^5$ ），存储和直接求逆或分解 Hessian 矩阵在算力和显存上是不可接受的 。



### 2. 不精确牛顿法（Inexact Newton / Newton-CG）

为了扩展到超大规模问题，工程上不再追求**精确**求解方程组，而是利用 **共轭梯度法（CG）** 等迭代方法进行近似求解，从而将空间复杂度降到 $O(n)$ 。

令残差向量为 ：


$$r_k = \nabla^2 f(x_k) p_k + \nabla f(x_k)$$

我们通过强制让残差的大小受梯度的约束来进行截断 ：


$$\|r_k\| \le \eta_k \|\nabla f(x_k)\| [cite_start]\quad (0 \le \eta_k < 1)$$



#### 残差强度的因果收敛律：

* 若 $\eta_k \le t < 1$ （常数），算法维持 **线性收敛** 速度 。


* 若 $\lim_{k\to\infty} \eta_k = 0$ ，算法达到 **超线性收敛** 速度 。


* 若 $\eta_k = O(\|\nabla f(x_k)\|)$ （如动态设 $\eta_k = \min(0.5, \|\nabla f(x_k)\|)$ ），且 Hessian 局部 Lipschitz，则可完美恢复 **二次收敛** 速度 ！



---

## 七、 鲁棒牛顿法：非正定海森矩阵的修正（Hessian Modification）

### 1. 现实中的灾难

在非凸优化或模型具有冗余特征时，理论假设 $\nabla^2 f(x) \succ 0$ 经常失效 ：

* **奇异（Singular）Hessian**：不可逆，牛顿步未定义 。


* **病态（Ill-conditioned）Hessian**：条件数极高，求逆带来巨大数值截断误差 。


* **不定（Indefinite）Hessian**：出现负特征值，牛顿方向可能变成**上升方向**，算法彻底崩溃 。



### 2. 正则化方案：海森矩阵扰动修正

在海森矩阵上人为加上一个对角正定扰动（Hessian Perturbation / Regularization） ：


$$\left( \nabla^2 f(x_k) + \mu I \right) d_k = - \nabla f(x_k) \quad (\mu > 0)$$



#### 这一修正带来的多重保证：

1. **数值稳定性**：有效改善矩阵条件数，保证 Cholesky 分解鲁棒进行 。


2. **绝对下降方向**：只要选择足够大的 $\mu$ 使得 $(\nabla^2 f(x_k) + \mu I) \succ 0$ ，就能 100% 确保 $\langle \nabla f(x_k), d_k \rangle < 0$ 。



### 3. 数学桥梁：双方法之间的几何插值

改进后的牛顿步精妙地融合了两种最基础的优化思想 ：

* **当 $\mu \to 0$ 时**：回归到 **纯牛顿法**，完全借助二阶曲率实现超快局部收敛 。


* **当 $\mu \to \infty$ 时**：步长变为 $d_k \approx -\frac{1}{\mu} \nabla f(x_k)$ ，这正是带有极小步长的 **梯度下降法** 。



> 这一思想也是 **Levenberg-Marquardt (LM) 算法** 以及 **信赖域方法（Trust Region Methods）** 的基石 。
> 
> 

4. 工业界如何选择参数 $\mu$ 的实用策略 

* **启发式试错（Trial and Error）**：先尝试 $\mu=0$ 进行 Cholesky 分解；若失败则置 $\mu = 10^{-3}$；再失败则每次放大10倍（ $\mu \leftarrow 10\mu$ ）直到成功。该方法代码实现极其简单 。


* **谱分析法（Spectral Method）**：理论上我们需要 $\mu > \max(0, -\lambda_{\min}(\nabla^2 f))$ 。对于大规模矩阵而言计算所有特征值成本太高，主要用于理论解释 。


* **修正 Cholesky 分解（Modified Cholesky）**：**工业界求解器的绝对标准**。在进行矩阵分解的同时，动态地在对角线元素上检测并加上扰动，兼顾了极高的计算效率与无懈可击的数值稳定性 。



---

## 核心方法对比总结表

| 特性 / 维度 | 梯度下降法 (Gradient Descent)  | 纯牛顿法 (Pure Newton)  | 阻尼牛顿法 (Damped Newton)  | 不精确牛顿法 (Newton-CG)  |
| --- | --- | --- | --- | --- |
| **收敛速度** | 线性收敛 (慢)  | 局部二次收敛 (极快)  | 全局收敛 + 后期二次收敛  | 超线性 / 二次收敛  |
| **单步空间复杂度** | <br>$O(n)$  | <br>$O(n^2)$  | <br>$O(n^2)$  | <br>$O(n)$ （无需显式存海森）  |
| **单步时间开销** | $O(n)$ | <br>$O(n^3)$  | <br>$O(n^3)$  | $k \times O(n^2)$ (依赖CG迭代步数) |
| **全局收敛性** | 具备 | <br>**不具备**（易发散）  | 具备（由回溯线搜索保证）  | 具备 |
| **仿射不变性** | 不具备  | 具备  | 具备  | 部分具备 |
| **适用场景** | 超大规模问题、简单易用 | 小规模二次可微强凸问题 | 中小规模、高质量凸优化 | <br>**工业级大规模非线性优化**  |
