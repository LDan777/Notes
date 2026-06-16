
# 第十讲：从梯度到次梯度

在传统的可微凸优化中，**梯度（Gradient）** 是核心工具。然而，实际工程和数据科学问题中经常遇到不可微（Nondifferentiable）的凸函数（例如 $L_1$ 正则化 $|x|_1$、最大值函数等） 。当微分工具失效时，我们需要引入次梯度（Subgradient）**与**次微分（Subdifferential）来接过凸优化的大旗 。

---

## 第一部分：次梯度的几何直觉与代数定义

### 1. 铺垫：可微凸函数的基石不等式

对于一个定义在凸集上且可微（Differentiable）的凸函数 $f$ ，其最核心的性质是其一阶泰勒展开构成了全局下界 ：


$$f(y) \ge f(x) + \nabla f(x)^T(y-x), \quad \forall y \in \text{dom}(f)$$



* **几何意义**：向量 $(\nabla f(x), -1)$ 定义了函数图像在点 $(x, f(x))$ 处的一条**非垂直支撑超平面（Supporting Hyperplane）** 。也就是说，函数的上方图形（Epigraph, 记作 $\text{epi}(f)$ ）完全位于这个超平面的上方 。


* **代数表达**：对于所有 $\text{epi}(f)$ 中的点 $(y, t)$ （即满足 $t \ge f(y)$ ），均有 ：



$$\begin{pmatrix} \nabla f(x) \\ -1 \end{pmatrix}^T \left( \binom{y}{t} - \binom{x}{f(x)} \right) \le 0$$






### 2. 核心定义：次梯度（Subgradient）

如果函数 $f$ 在点 $x$ 处**不可微**，我们无法计算 $\nabla f(x)$ 。但如果我们能找到一个向量 $g$ ，使得类似的全局下估计性质依然成立，这个向量 $g$ 就被称为 $f$ 在 $x$ 处的一个**次梯度** ：

> [!IMPORTANT]
> 
> 
> **次梯度代数定义** 向量 $g$ 是函数 $f$ 在点 $x$ 处的次梯度，当且仅当满足以下次梯度不等式 ：
> 
> 
> 
> $$f(y) \ge f(x) + g^T(y-x), \quad \forall y \in \text{dom}(f)$$
> 
> 
> 
> 
> 

* **本质区别**：
* **梯度** $\nabla f(x)$ ：是唯一的，代表函数在某点处的切线/切平面斜率 。


* **次梯度** $g$ ：**不一定唯一**。在函数不光滑的“拐点”（尖点）处，可能存在无数个支撑方向，它们都能满足全局下估计的要求 。





### 3. 几何支撑：上方图（Epigraph）与支撑超平面

次梯度的几何定义与函数的上方图紧密相连 ：


$$g \text{ 是 } f \text{ 在 } x \text{ 处的次梯度} \iff \binom{g}{-1} \text{ 定义了 } \text{epi}(f) \text{ 在 } (x, f(x)) \text{ 处的非垂直支撑超平面}$$



$$\text{即：} \binom{g}{-1}^T \left( \binom{y}{t} - \binom{x}{f(x)} \right) \le 0, \quad \forall (y, t) \in \text{epi}(f)$$



**💡 推导细节：为什么这两个定义完全等价？**

1. **从代数到几何**：已知 $f(y) \ge f(x) + g^T(y-x)$ 。因为 $(y, t) \in \text{epi}(f) \implies t \ge f(y)$ ，所以有 $t \ge f(x) + g^T(y-x)$ 。移项后得到 $g^T(y-x) - (t - f(x)) \le 0$ 。这正好可以写成内积形式： $\binom{g}{-1}^T \binom{y-x}{t-f(x)} \le 0$ 。


2. **从几何到代数**：若上述内积对所有 $t \ge f(y)$ 成立，那么取临界点 $t = f(y)$ ，代入即得 $g^T(y-x) - f(y) + f(x) \le 0 \implies f(y) \ge f(x) + g^T(y-x)$ 。证毕。



---

## 第二部分：次微分（Subdifferential）及其优良性质

### 1. 定义

函数 $f$ 在点 $x$ 处所有次梯度的**集合**称为**次微分**，记作 $\partial f(x)$ ：


$$\partial f(x) = \{ g \mid g^T(y-x) \le f(y) - f(x), \forall y \in \text{dom}(f) \}$$



### 2. 核心数学性质

* **闭凸性**： $\partial f(x)$ 必然是一个**闭凸集**（无论 $f$ 本身是否凸） 。


* *原因*：从定义式可以看出，对于每一个固定的 $y$ ，不等式 $g^T(y-x) \le f(y) - f(x)$ 都关于变量 $g$ 确定了一个闭半空间（Closed Halfspace）。因此， $\partial f(x)$ 是无数个闭半空间的交集，而半空间的交集必然是闭凸集 。




* **存在性与有界性**：如果 $f$ 是凸函数且 $x$ 属于其定义域的**内部（Open domain）**，则 $\partial f(x)$ **非空且有界**（即紧致凸集） 。


* *边界反例（空集情况）*：定义 $f:[0,1] \to [-1, 0]$, $f(x) = -x^{1/2}$ 。这是一个凸函数，但在边界点 $x=0$ 处，其切线斜率趋于 $-\infty$ ，你无法找到任何实数 $g$ 能够作为全局下估计，因此 $\partial f(0) = \emptyset$ 。




* **与梯度的关系**：若 $f$ 在 $x$ 处可微，则次微分集合退化为单元素集： $\partial f(x) = \{ \nabla f(x) \}$ 。



### 3. 两个经典解析范例

范例 A：绝对值函数 $f(x) = |x|$ 

* 当 $x > 0$ 时， $f(x)$ 可微， $\partial f(x) = \{1\}$ 。


* 当 $x < 0$ 时， $f(x)$ 可微， $\partial f(x) = \{-1\}$ 。


* 当 $x = 0$ 时，尖点处的次梯度需满足 $y \ge 0 + g \cdot (y-0) \implies |y| [cite_start]\ge g \cdot y$ 。


* 若 $y > 0$ ，则 $y \ge g y \implies g \le 1$ 。
* 若 $y < 0$ ，则 $-y \ge g y \implies g \ge -1$ 。
* 综上， $\partial f(0) = [-1, 1]$ 。



$$\partial |x| [cite_start]= \begin{cases} \{1\} & x > 0 \\ [-1, 1] & x = 0 \\ \{-1\} & x < 0 \end{cases}$$








范例 B：欧几里得范数 $f(x) = \|x\|_2$ 

$$\partial \|x\|_2 = \begin{cases} \frac{x}{\|x\|_2} & x \neq 0 \\ \{ g \mid \|g\|_2 \le 1 \} & x = 0 \end{cases}$$



* *推导逻辑*：在 $x=0$ 处，次梯度 $g$ 需满足 $\|y\|_2 \ge 0 + g^T(y-0) = g^Ty, \forall y$ 。根据柯西-施瓦茨不等式，要让 $g^Ty \le \|g\|_2\|y\|_2 \le \|y\|_2$ 对所有 $y$ 恒成立，当且仅当 $\|g\|_2 \le 1$ 。



---

## 第三部分：次微分运算法则（Subgradient Calculus）

在实际应用中，我们不可能总是用定义去推导次梯度。我们需要像高等数学中的求导法则一样，拥有一套代数运算法则 。

* **弱次微分运算法则**：只需找**一个**次梯度 $g \in \partial f(x)$ 的公式（对次梯度算法足够） 。


* **强次微分运学校则**：能够找出**整个集合** $\partial f(x)$ 的解析表达式 。



以下定理均假设 $f$ 为定义在开域上的凸函数 ：

| 运算类型 | 函数表达 | 次微分规则 $\partial g(x)$ 或 $\partial f(x)$ |
| --- | --- | --- |
| <br>**正正规放大**  | <br>$\alpha f(x) \quad (\alpha > 0)$  | <br>$\alpha \partial f(x)$  |
| <br>**线性加法** | <br>$f_1(x) + f_2(x)$  | <br>$\partial f_1(x) + \partial f_2(x)$ （集合闵可夫斯基和）  |
| <br>**仿射变换**  | <br>$f(Ax + b)$  | <br>$A^T \partial f(Ax + b)$  |
| <br>**有限点态最大值**  | <br>$\max_{i=1,\dots,m} f_i(x)$  | <br>$\text{Conv} \left( \bigcup_{i \in I(x)} \partial f_i(x) \right)$ ，其中 $I(x)$ 为当前激活集  |

### 💡 核心推导：有限点态最大值法则

设 $f(x) = \max \{ f_1(x), f_2(x) \}$ 。当在某点 $x$ 处 $f_1(x) = f_2(x)$ 时（两个函数相交），该点的次微分是什么？ 定理指出，它是所有**激活函数（Active functions）**次微分集合的**凸包（Convex Hull, 记作 Conv）** 。

* **激活集定义**： $I(x) = \{ i \mid f_i(x) = f(x) \}$ 。


* **公式含义**：若 $f_1, f_2$ 可微 ，在相交点处， $\partial f_1(x)=\{\nabla f_1(x)\}$ ， $\partial f_2(x)=\{\nabla f_2(x)\}$ 。那么 $f(x)$ 在该点的次微分就是这两个梯度向量连接而成的线段 ：



$$\partial f(x) = \{ \theta \nabla f_1(x) + (1-\theta) \nabla f_2(x) \mid 0 \le \theta \le 1 \}$$



---

## 第四部分：最优性条件（Optimality Conditions）

### 1. 无约束优化最优性条件

传统可微函数的极值点满足 $\nabla f(x) = 0$ 。在不可微凸优化中，它被完美泛化为：

> [!IMPORTANT]
> 
> 
> **费马引理的次梯度泛化** 
> $x^*$ 是无约束凸函数 $f(x)$ 的全局极小值点，当且仅当：
> 
> 
> 
> $$0 \in \partial f(x^*)$$
> 
> 
> 
> 
> 

* **极简证明**：由定义， $0 \in \partial f(x^*) \iff f(y) \ge f(x^*) + 0^T(y-x^*), \forall y \iff f(y) \ge f(x^*), \forall y$ 。这正是全局极小值的定义。



### 2. 约束优化的广义 KKT 条件

对于约束优化问题： $\min f_0(x) \quad \text{s.t.} \quad f_i(x) \le 0, \; i=1,\dots,m$ 。
若强对偶性成立，则 $x^*$ 和 $\lambda^*$ 分别为原始和对偶最优解的充要条件为 ：

1. **原始可行性**： $f_i(x^*) \le 0$ 


2. **对偶可行性**： $\lambda^* \ge 0$ 


3. **互补松弛性**： $\lambda_i^* f_i(x^*) = 0$ 


4. **拉格朗日函数极小化**： $x^*$ 必须是 $L(x, \lambda^*) = f_0(x) + \sum_{i=1}^m \lambda_i^* f_i(x)$ 的解 。用次微分表示即 ：



$$0 \in \partial L_x(x^*, \lambda^*) \subset \partial f_0(x^*) + \sum_{i=1}^m \lambda_i^* \partial f_i(x^*)$$






### 3. 引入示性函数与法锥（Normal Cone）

为了将**集合约束优化** $\min_{x \in C} f(x)$ 转化为无约束优化，我们引入**示性函数（Indicator Function）** $I_C(x)$ ：


$$I_C(x) = \begin{cases} 0 & x \in C \\ +\infty & x \notin C \end{cases}$$

此时问题等价于 $\min [f(x) + I_C(x)]$ 。根据无约束最优性条件，解 $x^*$ 满足：


$$0 \in \partial f(x^*) + \partial I_C(x^*)$$

而示性函数的次微分正是几何上的**法锥（Normal Cone）** $N_C(x)$ ：


$$\partial I_C(x) = N_C(x) = \{ g \in \mathbb{R}^n \mid g^T(y-x) \le 0, \; [cite_start]\forall y \in C \}$$



* **直观几何含义**：法锥内的向量与从 $x$ 指向集合 $C$ 内任意点 $y$ 的向量 $y-x$ 的夹角全部大于等于 $90^\circ$ 。


* **重要事实**：如果 $x$ 属于集合 $C$ 的**内部（Interior, $\text{int}(C)$ ）**，那么法锥退化为零向量集 $\{0\}$ 。



---

## 第五部分：正交投影（Orthogonal Projection）与支撑不等式

将点 $x$ 投影到闭凸集 $C$ 上的操作定义为 ：


$$\Pi_C(x) = \arg\min_{y \in C} \frac{1}{2}\|y-x\|_2^2 = \arg\min_{y \in \mathbb{R}^n} \frac{1}{2}\|y-x\|_2^2 + I_C(y)$$



### 💡 核心推导：投影的最优性一阶特征

根据刚才的法锥理论，上式一阶最优性条件为：


$$0 \in (y - x) + N_C(y) \implies x - y \in N_C(y)$$

根据法锥定义 $N_C(y) = \{ g \mid g^T(z-y) \le 0, \forall z \in C\}$ ，将 $g = x - y$ 代入，立得投影核心不等式 ：


$$(x - y)^T(z - y) \le 0, \quad \forall z \in C$$



### 💥 衍生重要几何不等式（次梯度算法的铁律）

将 $\Pi_X(y)$ 视为投影点，让任何集合内的点 $x \in X$ 作为上述的 $z$ ，将上面不等式改写为 ：


$$(y - \Pi_X(y))^T(x - \Pi_X(y)) \le 0$$

通过简单的代数凑项，我们可以得到两层极重要的不等式（常用于算法收敛性分析的距离项展开） ：

1. **第一层（强勾股定理不等式）**：


$$\|\Pi_X(y) - x\|_2^2 + \|y - \Pi_X(y)\|_2^2 \le \|y - x\|_2^2$$





2. **第二层（非扩张性）**：直接丢掉左边第二项非负项，开平方得到：


$$\|\Pi_X(y) - x\|_2 \le \|y - x\|_2$$


*几何直觉*：**投影会让外部的点离集合内的点更近。** 



---

## 第六部分：投影次梯度法（Projected Subgradient Method）

### 1. 算法迭代步

针对问题 $\min_{x \in X} f(x)$ （ $X$ 为闭凸约束集） ：


$$\begin{aligned} y_{k+1} &= x_k - \alpha_k g_k \quad (\text{梯度更新步，其中 } g_k \in \partial f(x_k)) \\ x_{k+1} &= \Pi_X(y_{k+1}) \quad (\text{正交投影步}) \end{aligned}$$



### 2. ⚠️ 核心警示：次梯度法不是下降算法！

这是初学者最容易犯错的地方：**次梯度方向 $-g_k$ 不一定是函数值的下降方向！** 

* *范例证明*：考虑 $f(x_1, x_2) = |x_1| + [cite_start]2|x_2|$ 。在某点处，次梯度迭代可能会导致 $f(x_{k+1}) > f(x_k)$ 。


* *那它凭什么收敛？*：次梯度法不追求每一步**函数值**下降，它追求的是**当前点到对偶/原始最优解集 $x^*$ 的欧几里得距离**在统计趋势上不断缩短 ！因此，我们需要记录历史最好值： $f_{\text{best}}^k = \min_{i=1,\dots,k} f(x_i)$ 。



---

## 第七部分：收敛性分析（Convergence Analysis）深度数学推导

### 1. 基础假设设定

* **最优解存在**： $f(x^*) = f^* > -\infty$ 。


* **次梯度有界（Lipschitz 连续性）**： $\|g\|_2 \le G, \forall g \in \partial f \iff |f(x) - f(y)| [cite_start]\le G\|x-y\|_2$ 。


* **初始距离有界**： $R = \|x_1 - x^*\|_2$ 。



### 2. 核心递推不等式的建立

我们来推导第 $k$ 步的距离平方项：


$$\|x_{k+1} - x^*\|_2^2 = \|\Pi_X(x_k - \alpha_k g_k) - x^*\|_2^2$$

利用前面第五部分推导的投影非扩张性公式 $\|\Pi_X(y) - x\|_2 \le \|y - x\|_2$ ，我们有：


$$\|x_{k+1} - x^*\|_2^2 \le \|(x_k - \alpha_k g_k) - x^*\|_2^2$$


展开右边平方项：


$$\|(x_k - x^*) - \alpha_k g_k\|_2^2 = \|x_k - x^*\|_2^2 - 2\alpha_k g_k^T(x_k - x^*) + \alpha_k^2\|g_k\|_2^2$$

此时，利用次梯度的定义式： $f(x^*) \ge f(x_k) + g_k^T(x^* - x_k) \implies g_k^T(x_k - x^*) \ge f(x_k) - f^*$ 。代入上式：

$$\|x_{k+1} - x^*\|_2^2 \le \|x_k - x^*\|_2^2 - 2\alpha_k(f(x_k) - f^*) + \alpha_k^2 G^2$$



将该式从 $i=1$ 到 $k$ 累加，中间项发生**级联消去（Telescoping）** ：


$$\|x_{k+1} - x^*\|_2^2 \le \|x_1 - x^*\|_2^2 - 2\sum_{i=1}^k \alpha_i(f(x_i) - f^*) + G^2\sum_{i=1}^k \alpha_i^2$$

移项并舍去左边大于零的 $\|x_{k+1} - x^*\|_2^2$ 得到基础估计公式 ：


$$2\sum_{i=1}^k \alpha_i(f(x_i) - f^*) \le R^2 + G^2\sum_{i=1}^k \alpha_i^2$$

因为 $f(x_i) \ge f_{\text{best}}^k$ ，所以 $\sum_{i=1}^k \alpha_i(f(x_i) - f^*) \ge (f_{\text{best}}^k - f^*)\sum_{i=1}^k \alpha_i$ 。最终得到通用的核心上界公式 ：


$$f_{\text{best}}^k - f^* \le \frac{R^2 + G^2\sum_{i=1}^k \alpha_i^2}{2\sum_{i=1}^k \alpha_i}$$



---

### 3. 三大步长规则下的收敛性行为演分析

🛑 规则一：固定步长（Constant Step Size） $\alpha_k = \alpha$ 

* 
**公式代入**： $\sum_{i=1}^k \alpha_i = k\alpha$ ， $\sum_{i=1}^k \alpha_i^2 = k\alpha^2$ 。


* **结果**： $f_{\text{best}}^k - f^* \le \frac{R^2 + G^2 k \alpha^2}{2k\alpha} = \frac{R^2}{2k\alpha} + \frac{G^2\alpha}{2}$ 。


* **极限行为**：当 $k \to \infty$ 时，第一项趋于 0，第二项保持不变。因此右侧收敛到 $\frac{G^2\alpha}{2}$ 。


* **结论**：**不能保证收敛到精确最优解**，只会收敛到最优解的一个误差邻域内（邻域大小与步长 $\alpha$ 锁死） 。



🛑 规则二：固定步长长度（Constant Step Length） $\alpha_k = \frac{\gamma}{\|g_k\|_2}$ 

* **公式代入**：此时 $\alpha_k \|g_k\|_2 = \gamma$ （每一步行进的几何距离固定为 $\gamma$ ） 。


* **结果**：利用 $\|g_k\|_2 \le G$ ，类似推导可得极限值收敛到 $\frac{G\gamma}{2}$ 。

* **结论**：**同样无法保证收敛到最优解**，但在实际中能避免因次梯度过大或过小导致的步长失控 。



#### 🌟 规则三：非平方可积但渐进消减步长（Diminishing Step Size）

我们需要步长同时满足两个看似矛盾的性质 ：

1. $\lim_{k \to \infty} \alpha_k = 0$ （步长必须衰减，消除最后残留的晃动） 。


2. $\sum_{k=1}^\infty \alpha_k = \infty$ （衰减不能太快，必须有足够的“动力”让点跑完无限远的距离） 。



* **典型步长选择**： $\alpha_k = \frac{a}{\sqrt{k}}$ 


* **理论结论**：**可以完美保证收敛到精确最优解 $f^*$** 。


* **收敛速率**：
* 直接取历史最优值的速率为： $O\left(\frac{\ln k}{\sqrt{k}}\right)$ 。


* 若采用**后半段均值消减（Averaging from $k/2$ to $k$ ）**，速率可优化至理想凸优化的下界： $O\left(\frac{1}{\sqrt{k}}\right)$ 。





💎 规则四：当最优目标值 $f^*$ 已知时的 Polyak 步长 

如果我们提前知道了理论最优值 $f^*$ （例如某些可行性验证问题，最优目标值必然为 0） ，我们可以使用著名的 **Polyak 步长** ：


$$\alpha_k = \frac{f(x_k) - f^*}{\|g_k\|_2^2}$$



* *数学奥秘*：这个步长能够**精准最小化**单步递推不等式 $\|x_{k+1}-x^*\|_2^2 \le \|x_k-x^*\|_2^2 - 2\alpha_k(f(x_k)-f^*) + \alpha_k^2\|g_k\|_2^2$ 的右侧上界 。


* *代入化简*：将 $\alpha_k$ 代入后，单步递推直接变为 $\|x_{k+1}-x^*\|_2^2 \le \|x_k-x^*\|_2^2 - \frac{(f(x_k)-f^*)^2}{\|g_k\|_2^2}$ 。


* *最终速率*：递归展开能获得没有 $\ln k$ 拖累的干净速率： $f_{\text{best}}^k - f^* \le \frac{GR}{\sqrt{k}}$ 。



---

## 第八部分：典型课堂案例与工业级应用拓展

案例一：求凸集交集问题（Find Point in Intersection of Convex Sets） 

目标是找到一个点 $x \in \bigcap_{j=1}^m C_j$ 。我们可以将其构造成一个无约束的凸极大值最小化问题 ：


$$\min f(x) = \max \{ d_1(x), d_2(x), \dots, d_m(x) \}$$

其中 $d_j(x) = \inf_{y \in C_j} \|x-y\|_2$ 是点到集合的欧氏距离 。

* **次梯度计算**：根据最大值函数的次微分凸包法则，设当前点 $x_k$ 距离最远的集合是 $C_j$ （即激活集） ：



$$g_k = \nabla d_j(x_k) = \frac{x_k - P_{C_j}(x_k)}{\|x_k - P_{C_j}(x_k)\|_2}$$





* **步长应用**：显然由于我们希望点就在交集内，所以最优目标值 $f^* = 0$ 。同时由上式可知 $\|g_k\|_2 = 1$ 。


* **算法写出**：代入 Polyak 步长公式得到 $\alpha_k = f(x_k) = \|x_k - P_{C_j}(x_k)\|_2$ 。带入次梯度下降迭代 ：



$$x_{k+1} = x_k - \alpha_k g_k = x_k - \|x_k - P_{C_j}(x_k)\|_2 \cdot \frac{x_k - P_{C_j}(x_k)}{\|x_k - P_{C_j}(x_k)\|_2} = P_{C_j}(x_k)$$





* **结论**：这就是大名鼎鼎的**贪婪投影算法（Greedy Projection Algorithm）** 。当 $m=2$ 时，它直接退化为经典的**交替投影算法（Alternating Projections）** ：每次只需盯着最远的那个集合做正交投影即可 。



案例二：强凸函数下的次梯度改进（Strongly Convex Case） 

如果函数 $f$ 不仅仅是凸函数，还是强凸（Strongly Convex）的（具有强参数 $\mu$ ） ：
通过设计特制的动态步长 $\alpha_s = \frac{2}{\mu(s+1)}$ ，结合加权历史平均 $\hat{x}_k = \sum_{s=1}^k \frac{2s}{k(k+1)}x_s$ ，其次梯度算法的收敛速率可以从 $O(1/\sqrt{k})$ 暴增到 ：


$$f(\hat{x}_k) - f^* \le \frac{2G^2}{\mu(k+1)} = O\left(\frac{1}{k}\right)$$

这证明了函数的优良性质（如强凸性）能够极大地反哺次梯度算法的收敛效率 。
