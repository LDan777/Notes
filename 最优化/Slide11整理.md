
# 课堂笔记：近端梯度法 (Proximal Gradient Methods, PGM)

## 一、 核心动机与问题建模

### 1. 复合函数优化模型 (Composite Function)

在实际应用中，我们经常需要求解一类**无约束优化问题**，其目标函数可以拆分为两个部分 ：


$$\min_{x \in \mathbb{R}^n} f(x) = g(x) + h(x)$$

其中两部分具有完全不同的数学性质 ：

* $g(x)$ ：**可微的 (Differentiable)**，定义域为 $\mathbb{R}^n$ 。


* $h(x)$ ：**凸的 (Convex)**，但通常是**不可微的 (Non-differentiable)** 。



### 2. 为什么需要 PGM？（传统方法的局限性）

* **传统梯度下降法**：由于 $h(x)$ 不可微，无法直接求梯度 $\nabla f$ ，传统梯度法失效 。


* **次梯度法 (Subgradient Method)**：虽然可以处理不可微的 $h(x)$ ，但其收敛速率仅为 $O(1/\epsilon^2)$ （即 $O(1/\sqrt{k})$ ），速度**太慢** 。


* **PGM 的核心思想**：如果 $h(x)$ 具有某种**特殊结构**（即其**近端映射易于计算**），我们可以利用这种结构实现快速收敛，而不需要诉诸于缓慢的次梯度法 。



---

## 二、 算法推导：从投影梯度法到近端梯度法

为了理解 PGM 的由来，我们需要利用 **MM 算法（Majorization-Minimization，主函数最小化）** 的思想来进行数学重构。

### 1. 铺垫：投影梯度法 (Projected Gradient Method) 的 MM 诠释

考虑约束优化问题 $\min_{x \in X} g(x)$ 。其更新步骤为 ：


$$x_{k+1} = \Pi_{X}(x_k - t_k \nabla g(x_k))$$

引入示性函数 (Indicator Function) $I_X(u)$ ：


$$I_X(u) = \begin{cases} 0 & \text{if } u \in X \\ +\infty & \text{otherwise} \end{cases}$$

该更新步等价于在 $x_k$ 处对 $g(u)$ 进行**一阶泰勒展开**，并加上一个二次惩罚项（近端项）来限制步长 ：


$$x_{k+1} = \arg\min_{u} \left\{ g(x_k) + \nabla g(x_k)^T(u - x_k) + \frac{1}{2t_k}\|u - x_k\|^2 + I_X(u) \right\}$$

### 2. 核心推导：近端梯度法 (PGM) 的公式变形

将投影梯度法中的示性函数 $I_X(u)$ 替换为一般的凸函数 $h(u)$ ，就得到了 PGM 的基本定义 ：


$$x_k = \arg\min_{u} \left\{ g(x_{k-1}) + \nabla g(x_{k-1})^T(u - x_{k-1}) + \frac{1}{2t_k}\|u - x_{k-1}\|^2 + h(u) \right\}$$

#### 详细推导步骤（配方法）：

我们要证明上述定义等价于显式的近端算子形式 ：

1. 忽略与优化变量 $u$ 无关的常数项 $g(x_{k-1})$ ，目标函数简化为：

$$\arg\min_{u} \left\{ \nabla g(x_{k-1})^T(u - x_{k-1}) + \frac{1}{2t_k}\|u - x_{k-1}\|^2 + h(u) \right\}$$


2. 对前两项关于 $u$ 进行**配方**。注意到：

$$\frac{1}{2t_k} \|u - (x_{k-1} - t_k \nabla g(x_{k-1}))\|^2 = \frac{1}{2t_k} \left( \|u - x_{k-1}\|^2 + 2t_k \nabla g(x_{k-1})^T(u - x_{k-1}) + t_k^2 \|\nabla g(x_{k-1})\|^2 \right)$$



展开后移项可得：

$$\nabla g(x_{k-1})^T(u - x_{k-1}) + \frac{1}{2t_k}\|u - x_{k-1}\|^2 = \frac{1}{2t_k} \|u - (x_{k-1} - t_k \nabla g(x_{k-1}))\|^2 - \frac{t_k}{2}\|\nabla g(x_{k-1})\|^2$$


3. 再次由于 $\frac{t_k}{2}\|\nabla g(x_{k-1})\|^2$ 与 $u$ 无关，在 $\arg\min$ 中可以丢弃。于是公式完美转化为 ：



$$x_k = \arg\min_{u} \left\{ \frac{1}{2t_k} \|u - (x_{k-1} - t_k \nabla g(x_{k-1}))\|^2 + h(u) \right\}$$



### 3. 近端映射 (Proximal Mapping) 的定义

为了简化表达，定义凸函数 $h$ 的**近端映射算子**（Prox-operator）为 ：


$$\text{prox}_h(x) = \arg\min_{u} \left\{ h(u) + \frac{1}{2}\|u - x\|^2 \right\}$$

因此，PGM 的迭代步可以紧凑地写为 **“先做梯度下降，再做近端映射”** ：


$$x_k = \text{prox}_{t_k h} \left( x_{k-1} - t_k \nabla g(x_{k-1}) \right)$$

> 
> **统一性视角** ：
> 
> 
> * 当 $h(x) = 0$ 时， $\text{prox}_0(x) = x$ ，算法退化为**标准梯度下降法**。
> * 当 $h(x) = I_C(x)$ （示性函数）时， $\text{prox}_{I_C}(x) = P_C(x)$ ，算法退化为**投影梯度法**。
> 
> 

---

## 三、 常见算子的解析解 (Easy Proximal Mappings)

PGM 能够高效运行的前提是： $\text{prox}_{t_k h}(\cdot)$ 存在**解析解**或极易计算 。以下是课堂列举的核心实例：

| 函数 $h(x)$ 或 约束集 $C$ | 算子名称 / 数学表达式 | 解析解 / 投影计算公式 |
| --- | --- | --- |
| <br>**超平面 (Hyperplane)**  | $C = \{x \mid a^T x = b\}, a \neq 0$ | <br>$P_C(x) = x + \frac{b - a^T x}{\|a\|^2$  || <br>**仿射集 (Affine Set)**  | $C = \{x \mid Ax = b\}, \text{rank}(A)=p$ | <br>$P_C(x) = x + A^T(AA^T)^{-1}(b - Ax)$ （当 $p \ll n$ 或 $AA^T=I$ 时高效）  |
| <br>**非负象限 (Nonnegative Orthant)**  | $C = \mathbb{R}_+^n$ | <br>$P_C(x) = x_+ = \max\{x, 0\}$ |
| <br>**矩形闭集 (Rectangle)**  | $C = [l, u] = \{x \mid l \le x \le u\}$ | <br>$P_C(x)_i = \begin{cases} l_i & x_i \le l_i \\ x_i & l_i \le x_i \le u_i \\ u_i & x_i \ge u_i \end{cases}$  |
| <br>**概率单纯形 (Simplex)**  | $C = \{x \mid e^Tx = 1, x \ge 0\}$ | <br>$P_C(x) = (x - \lambda e)_+$ ，其中 $\lambda$ 由 $\sum_{i=1}^n \max\{0, x_i - \lambda\} = 1$ 确定  |
| <br>**二次函数**  | $f(x) = \frac{1}{2}x^TAx + b^Tx + c \quad (A \ge 0)$ | <br>$\text{prox}_{tf}(x) = (I + tA)^{-1}(x - tb)$  |
| <br>**$L_2$ 范数 (Euclidean Norm)**  | $f(x) = \|x\|$ | <br>$\text{prox}_{tf}(x) = \begin{cases} (1 - t/\|x\|_2)x & \text{if } \|x\|_2 \ge t \\ 0 & \text{otherwise} \end{ca$  |
| <br>**对数阻垒 (Log Barrier)**  | $f(x) = -\sum_{i=1}^n \log x_i$ | <br>$\text{prox}_{tf}(x)_i = \frac{x_i + \sqrt{x_i^2 + 4t}}{2}$  |
| <br>**$L_1$ 范数 ($L_1$-Norm)**  | $h(x) = \|x\|$ | <br>**软阈值算子 (Soft-thresholding / Shrinkage)**（见下方详解） |

重点推导： $L_1$ 范数的软阈值算子证明 

对于 $h(x) = \|x\|_1$ ，近端映射问题为 ：


$$\arg\min_{u} \left\{ t\|u\|_1 + \frac{1}{2}\|u - x\|^2 \right\} = \arg\min_{u} \sum_{i=1}^n \left( t|u_i| + \frac{1}{2}(u_i - x_i)^2 \right)$$

由于各项关于 $u$ 是**可分离的 (Separable)**，我们可以对每个分量 $u_i$ 独立求解 ：


$$\min_{u_i} \phi(u_i) = t|u_i| + \frac{1}{2}(u_i - x_i)^2$$


利用凸优化的一阶最优性条件（次微分包含 0）：


$$0 \in \partial \phi(u_i) = t \cdot \partial |u_i| + (u_i - x_i) \implies x_i - u_i \in t \cdot \partial |u_i|$$


其中 $\partial |u_i| = \begin{cases} \{1\} & u_i > 0 \\ \{-1\} & u_i < 0 \\ [-1, 1] & u_i = 0 \end{cases}$

* **情况 1**：若 $u_i > 0$ ，则 $x_i - u_i = t \implies u_i = x_i - t$ 。由于要求 $u_i > 0$ ，此情况必须满足 $x_i > t$ 。
* **情况 2**：若 $u_i < 0$ ，则 $x_i - u_i = -t \implies u_i = x_i + t$ 。由于要求 $u_i < 0$ ，此情况必须满足 $x_i < -t$ 。
* **情况 3**：若 $u_i = 0$ ，则 $x_i - 0 \in [-t, t] \implies -t \le x_i \le t$ 。

综合上述三种情况，得到**软阈值算子 $S_t(x)$** 的精确表达式 ：


$$[\text{prox}_{th}(x)]_i = S_t(x_i) = \begin{cases} x_i - t & \text{if } x_i \ge t \\ 0 & \text{if } -t \le x_i \le t \\ x_i + t & \text{if } x_i \le -t \end{cases}$$

---

## 四、 核心概念：梯度映射 (Gradient Mapping)

由于 $f = g + h$ 不可微，我们无法直接用 $\nabla f(x)$ 作为停止准则。为此，引入**梯度映射** $G_t(x)$ 作为替代工具 。

### 1. 定义

$$G_t(x) = \frac{1}{t} \left( x - \text{prox}_{th}(x - t\nabla g(x)) \right)$$

由此，PGM 的更新步可以形式化地写为类似于梯度下降的样式 ：


$$x^+ = x - t G_t(x)$$



### 2. 重要性质与数学含义

> 
> **注意**： $G_t(x)$ 并不是 $f$ 的梯度或次梯度 ！但它满足以下关系：
> 
> 
> 
> $$G_t(x) \in \nabla g(x) + \partial h(x - tG_t(x)) = \nabla g(x) + \partial h(x^+)$$
> 
> 
> 
> 
> 

* **最优性等价定理** ：



$$G_t(x) = 0 \iff x \text{ 是 } f(x) \text{ 的全局极小值点}$$


* **工程意义**：在实际算法中，我们可以安全地将 $\|G_t(x_k)\| [cite_start]\le \epsilon$ 作为迭代**停止准则 (Stopping Rule)** 。



---

## 五、 收敛性分析与数学证明 (凸函数情形)

### 1. 基础假设 (Assumptions)

* $g$ 是凸的，且其梯度 $\nabla g$ 满足 **Lipschitz 连续性**，常数为 $L$ ：



$$\|\nabla g(x) - \nabla g(y)\|_2 \le L \|x - y\|_2, \quad \forall x,y$$





* $g$ 是 **$m$-强凸的** ($m \ge 0$ ，若 $m=0$ 则退化为一般凸) ：



$$g(x) \ge g(y) + \nabla g(y)^T(x - y) + \frac{m}{2}\|x - y\|^2$$





* $h$ 是闭凸函数 。最优值 $f^*$ 有限且可达 。



### 2. 关键引理：Lipschitz 上界与基础不等式

由 $\nabla g$ 的 $L$-Lipschitz 连续性，可得著名的下降引理（Upper Bound） ：


$$g(y) \le g(x) + \nabla g(x)^T(y - x) + \frac{L}{2}\|y - x\|^2$$

令 $y = x^+ = x - tG_t(x)$ ，代入上式得到 ：


$$g(x - tG_t(x)) \le g(x) - t \nabla g(x)^T G_t(x) + \frac{t^2 L}{2}\|G_t(x)\|^2$$

若步长固定满足 $0 < t \le 1/L$ ，则 $\frac{t^2 L}{2} \le \frac{t}{2}$ ，不等式进一步放缩为 ：


$$g(x - tG_t(x)) \le g(x) - t \nabla g(x)^T G_t(x) + \frac{t}{2}\|G_t(x)\|^2 \quad \text{--- 不等式 (1)}$$



> 
> **核心推论不等式 (2)** ：
> 只要不等式 (1) 成立，对于任意的 $z$ ，均有：
> 
> 
> 
> $$f(x - tG_t(x)) \le f(z) + G_t(x)^T(x - z) - \frac{t}{2}\|G_t(x)\|^2 - \frac{m}{2}\|x - z\|^2 \quad \text{--- 不等式 (2)}$$
> 
> 
> 
> 
> 

### 3. 定理：单调递减性 (Descent Method) 的证明

**证明**：在不等式 (2) 中令 $z = x$ ，由于 $x^+ = x - tG_t(x)$ ，我们有 ：


$$f(x^+) \le f(x) + G_t(x)^T(x - x) - \frac{t}{2}\|G_t(x)\|^2 - \frac{m}{2}\|x - x\|^2$$

$$f(x^+) \le f(x) - \frac{t}{2}\|G_t(x)\|^2$$

因为 $t > 0$ 且 $\|G_t(x)\|^2 \ge 0$ ，所以 $f(x^+) \le f(x)$ 。这证明了 PGM 是一个**严格下降算法** 。

### 4. 定理：单步性能提升与距离收缩放缩

**证明**：在不等式 (2) 中令 $z = x^*$ （最优解），则 ：


$$f(x^+) - f^* \le G_t(x)^T(x - x^*) - \frac{t}{2}\|G_t(x)\|^2 - \frac{m}{2}\|x - x^*\|^2$$


利用恒等式变形（将交叉项化为平方差）：


$$G_t(x)^T(x - x^*) - \frac{t}{2}\|G_t(x)\|^2 = \frac{1}{2t} \left( \|x - x^*\|^2 - \|x - x^* - tG_t(x)\|^2 \right)$$

注意到 $x - tG_t(x) = x^+$ ，代入合并同类项 ：


$$f(x^+) - f^* \le \frac{1}{2t} \left( (1 - mt)\|x - x^*\|^2 - \|x^+ - x^*\|^2 \right) \quad \text{--- 不等式 (3)}$$

由于 $m \ge 0$ ，可以丢弃强凸项得到更宽泛的不等式 ：


$$f(x^+) - f^* \le \frac{1}{2t} \left( \|x - x^*\|^2 - \|x^+ - x^*\|^2 \right) \quad \text{--- 不等式 (4)}$$



> 
> **推论** ：由于左侧 $f(x^+) - f^* \ge 0$ ，必然有 $\|x^+ - x^*\|_2 \le \|x - x^*\|_2$ 。这表明**当前点到最优解集的欧氏距离是单调不增的** 。
> 
> 

### 5. 最终收敛速率推导

场景 A：一般凸函数 ($m = 0$) 的 $O(1/k)$ 收敛速率 

**推导步骤**：

1. 设定固定步长 $t = 1/L$ 。将第 $i$ 步的放缩不等式 (4) 写出 ：



$$f(x_i) - f^* \le \frac{L}{2} \left( \|x_{i-1} - x^*\|^2 - \|x_i - x^*\|^2 \right)$$


2. 对 $i = 1, 2, \dots, k$ 的所有不等式进行**裂项求和（Telescoping Sum）** ：



$$\sum_{i=1}^k (f(x_i) - f^*) \le \frac{L}{2} \sum_{i=1}^k \left( \|x_{i-1} - x^*\|^2 - \|x_i - x^*\|^2 \right) = \frac{L}{2} \left( \|x_0 - x^*\|^2 - \|x_k - x^*\|^2 \right) \le \frac{L}{2}\|x_0 - x^*\|^2$$





3. 利用算法的单调递减性，必有 $f(x_k) \le f(x_i)$ （对于所有 $i \le k$ ） 。因此 ：



$$k (f(x_k) - f^*) \le \sum_{i=1}^k (f(x_i) - f^*) \le \frac{L}{2}\|x_0 - x^*\|^2$$


4. 两边同除以 $k$ ，最终得到 ：



$$f(x_k) - f^* \le \frac{L}{2k}\|x_0 - x^*\|^2$$


**结论**：一般凸情形下，PGM 具有 **$O(1/k)$** 的亚线性收敛速率，达到 $\epsilon$-精度需要 $O(1/\epsilon)$ 次迭代 。



#### 场景 B：强凸函数 ($m > 0$) 的线性收敛速率 (Linear Convergence)

**推导步骤**：

1. 因为 $f(x^+) \ge f^*$ ，将此代入不等式 (3) 的左侧 ：



$$0 \le \frac{1}{2t} \left( (1 - mt)\|x - x^*\|^2 - \|x^+ - x^*\|^2 \right) \implies \|x^+ - x^*\|^2 \le (1 - mt)\|x - x^*\|^2$$





2. 在固定步长 $t = 1/L$ 下，递推 $k$ 次得到 ：



$$\|x_k - x^*\|^2 \le \left( 1 - \frac{m}{L} \right)^k \|x_0 - x^*\|^2$$


**结论**：若 $g$ 强凸（即 $m > 0$ ），算法呈现**线性收敛**（几何级数速度收敛） 。



---

## 六、 步长策略：回溯线搜索 (Backtracking Line Search)

如果 Lipschitz 常数 $L$ 未知或难以计算，必须使用**回溯线搜索**来自适应寻找步长 $t$ 。

### 1. 线搜索算法逻辑

1. 给定初始步长 $\hat{t} > 0$ 以及衰减因子 $\beta \in (0, 1)$ 。


2. **循环条件**：不断进行回溯（令 $t := \beta t$ ），直到满足以下**充分下降不等式** ：



$$g(x - tG_t(x)) \le g(x) - t \nabla g(x)^T G_t(x) + \frac{t}{2}\|G_t(x)\|^2$$





3. 退出循环时，所选取的步长必然满足下界保护 ：



$$t \ge t_{min} = \min\left\{\hat{t}, \frac{\beta}{L}\right\}$$






### 2. 线搜索下的收敛性保证

由于每一步都强制满足了基本不等式 (1)，因此收敛性分析完全可以平行平移 。只需将固定步长 $t$ 替换为下界 $t_{min}$ 即可 ：


$$f(x_k) - f^* \le \frac{1}{2k t_{min}} \|x_0 - x^*\|^2 \quad \text{(一般凸情形下的 } O(1/k) \text{ 速率)}$$



$$\|x_k - x^*\|^2 \le (1 - m t_{min})^k \|x_0 - x^*\|^2 \quad \text{(强凸情形下的线性收敛速率)}$$



---

## 七、 经典应用案例

### 1. 案例一：带箱约束的凸二次规划 (QP with Box Constraints)

$$\min_x \frac{1}{2}x^TAx + b^Tx \quad \text{s.t. [cite_start]} 0 \le x \le 1$$



* **模型拆解**： $g(x) = \frac{1}{2}x^TAx + b^Tx$ （可微）， $h(x) = I_{[0,1]^n}(x)$ （不可微示性函数） 。


* **PGM 迭代步**：由于 $h$ 的近端映射是向全空间立方体的投影，根据矩形闭集的截断公式，算法更新为 ：



$$x_{k+1} = P_{[0,1]^n} \left( x_k - t_k (Ax_k + b) \right) = \max\left\{0, \min\left\{1, x_k - t_k (Ax_k + b)\right\}\right\}$$



### 2. 案例二：用于 LASSO 问题的 ISTA 算法 (Iterative Shrinkage-Thresholding Algorithm)

$$\min_x \frac{1}{2}\|Ax - b\|^2 + \lambda \|x\|_1$$



* **模型拆解** ：


* $g(x) = \frac{1}{2}\|Ax - b\|^2 \implies \nabla g(x) = A^T(Ax - b)$ 


* $h(x) = \lambda \|x\|_1 \implies \text{近端映射对应为参数为 } \lambda t \text{ 的软阈值算子 } S_{\lambda t}(\cdot)$ 




* **Lipschitz 常数计算**： $\nabla^2 g(x) = A^TA \implies L = \lambda_{max}(A^TA)$ 。


* **ISTA 精确迭代公式**（取固定步长 $t = 1/L$ ） ：



$$x_{k+1} = S_{\lambda / L} \left( x_k - \frac{1}{L}A^T(Ax_k - b) \right)$$






---

## 八、 扩展：非凸函数情形下的收敛性定理

即使当 $g(x)$ 是**非凸函数**时，近端梯度法（采用固定步长 $t \in (0, 1/L)$ 或回溯线搜索）依然具有非常优秀的收敛性质 。其核心收敛定理如下 ：

1. **单调递减性** ：序列 $\{f(x_k)\}_{k \ge 0}$ 单调不增 。且 $f(x_{k+1}) < f(x_k)$ 当且仅当 $x_k$ 不是驻点（Stationary Point） 。


2. **梯度映射渐进稳定性** ：随着迭代进行，梯度映射渐进趋于 0：



$$G_{t_k}(x_k) \rightarrow 0 \quad \text{as } k \rightarrow \infty$$





3. **有限迭代下的全局最差上限** ：存在正状态常数 $M$ ，使得前 $k$ 步中最小的梯度映射模长满足：



$$\min_{i=0,1,\dots,k} \|G_{t_i}(x_i)\| [cite_start]\le \frac{\sqrt{f(x_0) - f^*}}{\sqrt{M(k+1)}}$$





4. **驻点收敛性** ：由 PGM 算法生成的序列 $\{x_k\}_{k \ge 0}$ 的**所有极限点（Limit Points）全部都是驻点** 。
