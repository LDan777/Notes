# 第7讲：梯度下降法（Gradient Descent Methods）详细课堂笔记

对应PPT：Lecture 7: Gradient Descent Methods
授课教师：姜儒俊（JIANG, Rujun）
课程方向：最优化（Optimization）

---

# 1. 迭代优化算法（Iterative Optimization Algorithm）

## 1.1 无约束优化问题（Unconstrained Optimization Problem）

本节课研究的问题是：

 $$
\min_{x \in \mathbb{R}^n} f(x)
 $$

即：

在  $  n  $  维实数空间中，寻找一个点  $  x  $ ，使目标函数（objective function）  $  f(x)  $  最小。

这里：

*  $  x  $ ：优化变量（optimization variable）
*  $  f(x)  $ ：目标函数（objective function）
*  $  \mathbb{R}^n  $ ：n维实数空间

这是最优化中最经典的一类问题。

对应PPT页码：2页

---

## 1.2 迭代算法框架（Iterative Algorithm Framework）

很多优化算法都采用"迭代（iteration）"思想。

即：

从一个初始点（initial point）开始，不断更新。

一般形式：

 $$
x_{k+1}=x_k+t_kp_k
 $$

其中：

*  $  x_k  $ ：第k次迭代点（iterate）
*  $  p_k  $ ：搜索方向（search direction）
*  $  t_k  $ ：步长（step size）

含义：

从当前位置  $  x_k  $ ，沿方向  $  p_k  $  走一步，长度为  $  t_k  $ 。

于是得到新的点：

 $$
x_{k+1}
 $$

如果：

 $$
p_k^T\nabla f(x_k)<0
 $$

则  $  p_k  $  被称为下降方向（descent direction）。

原因：

梯度（gradient）指向函数增长最快方向。

因此负梯度方向（negative gradient direction）会使函数下降最快。

对应PPT页码：2页

---

# 2. 梯度下降法（Gradient Descent Method）

## 2.1 梯度下降法的核心思想

梯度下降法（Gradient Descent, GD）是最经典的一阶优化方法（first-order optimization method）。

它只利用：

* 函数值（function value）
* 一阶导数（gradient）

不需要二阶导数（Hessian）。

其核心思想是：

每一步都沿着函数下降最快方向前进。

即：

 $$
p_k=-\nabla f(x_k)
 $$

于是更新公式变成：

 $$
x_{k+1}=x_k-t_k\nabla f(x_k)
 $$

这就是经典梯度下降法。

对应PPT页码：4页

---

## 2.2 为什么负梯度方向下降最快？（Steepest Descent Direction）

梯度：

 $$
\nabla f(x)
 $$

表示函数在当前位置增长最快方向。

因此：

 $$
-\nabla f(x)
 $$

就是下降最快方向。

老师用"浓雾中的下山"（Foggy Mountain）进行直观解释。

对应PPT页码：3页

### 直观理解

想象：

你站在一座山上，周围全是雾。

你看不到全局地形。

你只能感受到脚下地面的倾斜方向。

这时：

* 梯度方向：最陡上坡方向
* 负梯度方向：最陡下坡方向

于是你每一步都朝最陡下坡方向走。

不断重复。

最终走到谷底。

这就是梯度下降法。

---

## 2.3 梯度下降法的另一种解释：局部近似（Local Approximation）

老师还给出了一个非常重要的理解方式。

梯度下降法其实是在最小化下面这个式子：

 $$
f(x_k)+\nabla f(x_k)^T(x-x_k)+\frac{1}{2t_k}|x-x_k|^2
 $$

它由两部分组成：

### 第一部分：函数线性化（Linearization）

 $$
f(x_k)+\nabla f(x_k)^T(x-x_k)
 $$

这是函数在当前点的"一阶泰勒展开（first-order Taylor expansion）"。

表示：

在当前点附近，用一个平面近似原函数。

---

### 第二部分：邻近项（Proximal Term）

 $$
\frac{1}{2t_k}|x-x_k|^2
 $$

作用：

不让新的点离当前点太远。

否则仅靠线性化可能会走飞。

因此：

梯度下降法实际上是在：

"局部线性近似 + 防止步子太大"

之间做平衡。

这是现代优化中非常重要的思想。

对应PPT页码：4页

---

# 3. 终止条件（Termination Criteria）

## 3.1 为什么不能真的等梯度等于0？

理论上：

最优点满足：

 $$
\nabla f(x)=0
 $$

但计算机中存在浮点误差（floating point error）。

因此：

不可能真正等于0。

只能"足够小"。

对应PPT页码：5页

---

## 3.2 常见终止条件

### （1）梯度足够小

 $$
|\nabla f(x_k)|\le \epsilon
 $$

表示已经接近驻点（stationary point）。

这是最常见的终止条件。

---

### （2）函数值变化很小

 $$
|f(x_{k+1})-f(x_k)|\le \epsilon
 $$

说明函数几乎不再下降。

也可以使用相对误差：

 $$
\frac{|f(x_{k+1})-f(x_k)|}{|f(x_k)|}\le \epsilon
 $$

---

### （3）相邻迭代点变化很小

 $$
|x_{k+1}-x_k|\le \epsilon
 $$

说明算法已经几乎不移动。

对应PPT页码：5页

---

# 4. 步长选择（Step Size Rule）

步长（step size）是梯度下降法最核心的问题之一。

方向决定"往哪走"。

步长决定"走多远"。

如果步长太小：

* 收敛很慢

如果步长太大：

* 可能震荡
* 甚至发散（diverge）

对应PPT页码：6页

---

## 4.1 固定步长（Constant Step Size）

即：

 $$
t_k=\alpha
 $$

其中：

 $$
\alpha>0
 $$

是固定常数。

优点：

* 简单
* 易实现
* 计算快

缺点：

* 很依赖参数选择
* 太大会发散
* 太小会很慢

老师特别提到：

常见选择：

 $$
t=\frac{1}{L}
 $$

这里  $  L  $  是Lipschitz常数。

这是理论分析中最经典的步长。

---

## 4.2 精确线搜索（Exact Line Search）

思想：

沿当前下降方向，精确寻找最优步长。

即：

 $$
t_k=\arg\min_{t\ge0}f(x_k+t p_k)
 $$

也就是说：

在这条直线上，把函数值降到最小。

优点：

* 每一步下降最多

缺点：

* 每一步都要解一个一维优化问题
* 代价较高

对应PPT页码：6页

---

## 4.3 回溯线搜索（Backtracking Line Search）

这是实际最常用的方法。

对应PPT页码：9页

核心思想：

先尝试一个较大步长。

如果不满足下降条件。

就不断缩小。

### 算法流程

初始：

 $$
t=\hat t
 $$

然后不断乘：

 $$
\beta\in(0,1)
 $$

通常：

 $$
\beta=0.5
 $$

直到满足Armijo条件：

 $$
f(x_k+t_kp_k)\le f(x_k)+\alpha t_kp_k^T\nabla f(x_k)
 $$

其中：

*  $  \alpha  $ ：小正数
* 常取  $  10^{-4}  $ 

---

## 4.4 Armijo条件（Armijo Rule）直观理解

右边：

 $$
f(x_k)+\alpha t_kp_k^T\nabla f(x_k)
 $$

代表：

"你至少应该下降这么多"。

如果实际下降不足。

说明步长太大。

就继续缩小。

老师课堂里推导了回溯线搜索下步长存在下界：

 $$
t_k\ge \min\left{\hat t,\frac{2(1-\alpha)\beta}{L}\right}
 $$

这说明：

步长不会无限缩小。

因此算法可以正常运行。

---

# 5. Lipschitz连续（Lipschitz Continuity）

这是本节课最核心的理论基础。

对应PPT页码：11页

---

## 5.1 Lipschitz连续定义

函数  $  h  $  是Lipschitz连续的，如果：

 $$
|h(x)-h(y)|\le L|x-y|
 $$

其中：

*  $  L  $ ：Lipschitz常数（Lipschitz constant）

含义：

函数变化速度不会无限大。

即：

函数不会突然特别陡。

---

## 5.2 梯度Lipschitz连续（L-smooth）

本节更重要的是：

梯度满足Lipschitz连续。

即：

 $$
|\nabla f(x)-\nabla f(y)|\le L|x-y|
 $$

又称：

* L-smooth
* 光滑函数（smooth function）

老师特别强调：

L越小。

函数越"好"。

越容易分析收敛性。

L越大。

函数变化越剧烈。

优化越困难。

---

## 5.3 二次上界（Quadratic Upper Bound）

这是本节最重要的不等式之一。

如果梯度是Lipschitz连续的。

则：

 $$
f(y)\le f(x)+\nabla f(x)^T(y-x)+\frac{L}{2}|x-y|^2
 $$

对应PPT页码：12页

老师强调：

这个式子意味着：

任何L-smooth函数。

都存在一个二次函数作为它的上界。

并且：

在点  $  x  $  处相切。

这是后面所有收敛分析的核心工具。

---

## 5.4 Hessian与Lipschitz的关系

如果函数二阶可导：

 $$
|\nabla^2f(x)|\le L
 $$

等价于：

梯度Lipschitz连续。

这里：

*  $  \nabla^2f(x)  $ ：Hessian矩阵

即：

L实际上控制的是"曲率（curvature）"。

---

# 6. 梯度下降法收敛性分析（Convergence Analysis）

对应PPT页码：13-15页

---

## 6.1 充分下降引理（Sufficient Decrease Lemma）

这是整个分析核心。

结论：

 $$
f(x)-f(x-t\nabla f(x))
\ge
t\left(1-\frac{Lt}{2}\right)|\nabla f(x)|^2
 $$

老师反复强调：

这个式子表示：

每一步至少下降多少。

---

## 6.2 为什么需要  $  t<2/L  $ 

为了让下降量为正：

需要：

 $$
1-\frac{Lt}{2}>0
 $$

即：

 $$
t<\frac{2}{L}
 $$

因此：

如果步长超过  $  2/L  $ 。

算法可能不下降。

甚至发散。

这是梯度下降法最经典稳定性条件。

---

## 6.3 最经典步长：  $  t=1/L  $ 

此时：

 $$
t\left(1-\frac{Lt}{2}\right)
 $$

达到最大值。

因此：

 $$
t=\frac1L
 $$

是理论中最经典选择。

老师课堂专门分析了这个二次函数。

指出：

它在  $  t=1/L  $  处达到最大。

---

## 6.4 梯度范数收敛

由充分下降引理。

可以推出：

 $$
\nabla f(x_k)\to0
 $$

即：

梯度最终趋于0。

说明算法会趋近驻点。

但是：

这里只能保证：

收敛到驻点（stationary point）。

不能保证一定是全局最优。

尤其在非凸问题（nonconvex optimization）中。

对应PPT页码：15页

---

## 6.5 非凸情况下的收敛速度

可以得到：

 $$
\min_{i=0,1,...,k}|\nabla f(x_i)|
\le
\sqrt{\frac{f(x_0)-f^*}{(k+1)M}}
 $$

因此：

收敛速度为：

 $$
O\left(\frac1{\sqrt k}\right)
 $$

这是次线性收敛（sublinear convergence）。

老师特别提醒：

这个结果是"平均意义"上的收敛。

不是说第k步一定达到。

而是前k步中最好的那个梯度。

---

# 7. 凸函数（Convex Function）下的收敛

对应PPT页码：16页

---

## 7.1 凸函数的重要性质

凸函数最关键特点：

局部信息可以反映全局信息。

即：

局部最优 = 全局最优。

这也是为什么凸优化容易分析。

---

## 7.2 凸函数下梯度法收敛速度

结论：

 $$
f(x_k)-f(x^*)
\le
\frac{1}{2kt}|x_0-x^*|^2
 $$

对应PPT页码：16页

于是：

收敛速度：

 $$
O\left(\frac1k\right)
 $$

这比非凸情况：

 $$
O(1/\sqrt k)
 $$

更快。

---

## 7.3 为什么凸函数更快？

老师在推导中使用了：

### （1）凸性不等式

 $$
f(y)\ge f(x)+\nabla f(x)^T(y-x)
 $$

### （2）平方范数恒等式

这是推导中的关键技巧。

最后得到：

一种"前后相消（telescoping）"结构。

于是：

中间项全部消掉。

只剩首尾两项。

因此能得到漂亮收敛结果。

这是优化理论中非常经典技巧。

---

## 7.4 Nesterov加速梯度法（Nesterov Accelerated Gradient）

老师简单介绍：

普通梯度法：

 $$
O(1/k)
 $$

Nesterov加速：

 $$
O(1/k^2)
 $$

更快。

核心思想：

利用前一步信息进行"动量（momentum）"加速。

老师提到：

如果后面有时间会详细讲。

---

# 8. 强凸函数（Strongly Convex Function）

对应PPT页码：17页

---

## 8.1 强凸定义（Strong Convexity）

函数  $  f  $  是  $  \mu  $ -强凸的，如果：

 $$
f(x)-\frac\mu2x^Tx
 $$

是凸函数。

其中：

*  $  \mu>0  $ 

---

## 8.2 强凸的一阶条件

 $$
f(y)
\ge
f(x)+\nabla f(x)^T(y-x)
+
\frac\mu2|x-y|^2
 $$

老师特别强调：

相比普通凸函数。

这里多了一个：

二次项下界。

意味着：

函数至少像二次函数那样弯曲。

因此：

不会太平。

---

## 8.3 Hessian条件

如果二阶可导：

 $$
\nabla^2f(x)\succeq \mu I
 $$

表示：

Hessian最小特征值至少为  $  \mu  $ 。

因此函数具有足够曲率。

---

# 9. 强凸情况下的线性收敛（Linear Convergence）

这是本节最重要结论之一。

对应PPT页码：17页

---

## 9.1 线性收敛速度

结论：

 $$
f(x_k)-f^*
\le
\left(1-\frac\mu L\right)^k
(f(x_0)-f^*)
 $$

注意：

这里是指数下降。

因为：

 $$
(1-c)^k
 $$

会非常快趋于0。

因此：

比  $  1/k  $  快得多。

---

## 9.2 为什么叫线性收敛？

因为取log后：

 $$
\log e_k
 $$

与  $  k  $  呈线性关系。

因此叫：

* 线性收敛（linear convergence）
* 几何收敛（geometric convergence）

---

# 10. 条件数（Condition Number）

对应PPT页码：18-20页

---

## 10.1 条件数定义

 $$
\kappa=\frac{L}{\mu}
 $$

称为：

条件数（condition number）。

它决定梯度法速度。

---

## 10.2 条件数与复杂度

达到误差  $  \epsilon  $  所需迭代次数：

 $$
O\left(\frac{L}{\mu}\log\frac1\epsilon\right)
 $$

因此：

* 条件数越大
* 收敛越慢

---

## 10.3 几何直观（Geometric Intuition）

老师花了很多时间解释这个。

### 条件数小：

等高线接近圆形。

像一个圆碗。

梯度直接指向最优点。

因此：

收敛很快。

---

### 条件数大：

等高线像狭长峡谷。

梯度方向会不断左右震荡。

形成：

zig-zag（之字形）运动。

因此：

收敛非常慢。

老师举例：

 $$
f(x,y)=100x^2+y^2
 $$

会形成非常狭长峡谷。

对应PPT页码：8页、19页

---

## 10.4 为什么会慢？

核心原因：

不同方向曲率差异太大。

* 陡方向：需要小步长
* 平方向：需要大步长

但普通梯度法：

所有方向只能共用一个步长。

因此被最大曲率绑架。

只能取很小步长。

导致平坦方向前进极慢。

---

# 11. 预条件梯度法（Preconditioned Gradient Descent）

对应PPT页码：21页

---

## 11.1 核心思想

既然问题来自不同方向尺度不同。

那就对不同方向采用不同缩放。

更新公式：

 $$
x_{k+1}=x_k-t_kP\nabla f(x_k)
 $$

其中：

 $$
P\succ0
 $$

是正定矩阵（positive definite matrix）。

---

## 11.2 对角预条件（Diagonal Preconditioner）

常见选择：

 $$
P=\text{diag}(d_1,...,d_n)
 $$

直观理解：

* 陡方向：步长小
* 平方向：步长大

从而抵消条件数影响。

---

## 11.3 Hessian逆矩阵预条件

如果：

 $$
P=(\nabla^2f(x_k))^{-1}
 $$

则得到：

牛顿法（Newton's Method）。

老师说：

这几乎完美修正局部曲率。

因此收敛极快。

---

# 12. 收敛速度分类（Convergence Rate）

对应PPT页码：22-25页

---

## 12.1 常见误差定义

### 点误差

 $$
e_k=|x_k-x^*|
 $$

### 函数值误差

 $$
e_k=f(x_k)-f(x^*)
 $$

### 梯度误差

 $$
e_k=|\nabla f(x_k)|
 $$

---

## 12.2 次线性收敛（Sublinear Convergence）

若：

 $$
\lim_{k\to\infty}\frac{e_{k+1}}{e_k}=1
 $$

则称：

次线性收敛。

典型：

 $$
1/k
 $$

或者：

 $$
1/\sqrt k
 $$

---

## 12.3 线性收敛（Linear Convergence）

若：

 $$
\lim_{k\to\infty}\frac{e_{k+1}}{e_k}=\mu
 $$

其中：

 $$
0<\mu<1
 $$

则称：

线性收敛。

典型：

 $$
(1-c)^k
 $$

---

## 12.4 超线性收敛（Superlinear Convergence）

若：

 $$
\lim_{k\to\infty}\frac{e_{k+1}}{e_k}=0
 $$

则称：

超线性收敛。

---

## 12.5 二次收敛（Quadratic Convergence）

如果：

 $$
\frac{e_{k+1}}{e_k^2}
 $$

趋于常数。

则称：

二次收敛。

老师特别强调：

二次收敛极快。

比如误差：

 $$
10^{-1}
\rightarrow
10^{-2}
\rightarrow
10^{-4}
\rightarrow
10^{-8}
 $$

几步就能达到机器精度。

牛顿法就具有二次收敛。

---

# 13. 梯度法实现与复杂度（Implementation and Complexity）

对应PPT页码：26-27页

---

## 13.1 浮点运算（FLOP）

一次：

* 加法
* 减法
* 乘法
* 除法

称为：

一次浮点运算（floating-point operation, FLOP）。

算法复杂度就是：

总FLOP数量。

---

## 13.2 矩阵乘向量复杂度

若：

 $$
A\in\mathbb R^{m\times n}
 $$

则：

 $$
Ax
 $$

复杂度：

 $$
O(mn)
 $$

---

## 13.3 矩阵乘矩阵复杂度

复杂度：

 $$
O(mnl)
 $$

远高于矩阵乘向量。

因此：

优化算法里通常尽量避免矩阵乘矩阵。

---

## 13.4 重复计算缓存（Caching）

老师特别强调：

不要重复算已经算过的量。

例如线搜索中：

某些矩阵向量乘积可以存下来。

从而减少复杂度。

这是实际编程中非常重要技巧。

---

# 14. 逻辑回归（Logistic Regression）例子

对应PPT页码：28页

---

## 14.1 逻辑回归目标函数

 $$
\ell(x)
=
\frac1m
\sum_{i=1}^m
\log(1+\exp(-b_i a_i^Tx))
+
\lambda|x|^2
 $$

其中：

*  $  a_i  $ ：样本特征（feature）
*  $  b_i\in{-1,1}  $ ：标签（label）
*  $  \lambda|x|^2  $ ：正则项（regularization term）

---

## 14.2 为什么加正则项？

作用：

防止过拟合（overfitting）。

即：

避免模型过度适应训练集。

---

## 14.3 MATLAB实现技巧

老师介绍了：

如何通过向量化（vectorization）降低复杂度。

例如：

 $$
b\circ(Ax)
 $$

表示Hadamard积（Hadamard Product）。

即：

对应元素相乘。

在MATLAB里：

```matlab
b.*(A*x)
```

老师强调：

真正昂贵的是：

 $$
Ax
 $$

即矩阵乘向量。

其他逐元素运算都比较便宜。

因此实现中应重点减少矩阵乘法次数。

---

# 15. 本节课核心知识总结

## 15.1 本节课主线

本节课主要讲了：

1. 梯度下降法基本框架
2. 步长选择方法
3. Lipschitz连续
4. 收敛性分析
5. 凸优化与强凸优化
6. 条件数影响
7. 预条件方法
8. 收敛速度分类
9. 实际实现复杂度

---

## 15.2 最核心公式

### 梯度下降更新

 $$
x_{k+1}=x_k-t_k\nabla f(x_k)
 $$

---

### 二次上界

 $$
f(y)\le f(x)+\nabla f(x)^T(y-x)+\frac L2|x-y|^2
 $$

---

### 充分下降引理

 $$
f(x)-f(x-t\nabla f(x))
\ge
t\left(1-\frac{Lt}{2}\right)|\nabla f(x)|^2
 $$

---

### 凸函数收敛速度

 $$
O(1/k)
 $$

---

### 强凸函数收敛速度

 $$
O((1-\mu/L)^k)
 $$

---

### 条件数

 $$
\kappa=L/\mu
 $$

条件数越大。

梯度法越慢。

---

# 16. 容易混淆的重要概念辨析

## 16.1 Lipschitz连续 vs 强凸

### Lipschitz梯度

控制：

函数不能太陡。

是"上界"。

对应：

 $$
\nabla^2f(x)\preceq LI
 $$

---

### 强凸

控制：

函数不能太平。

是"下界"。

对应：

 $$
\nabla^2f(x)\succeq \mu I
 $$

---

## 16.2 驻点 vs 最优点

### 驻点（Stationary Point）

 $$
\nabla f(x)=0
 $$

只说明梯度为0。

不一定是最优。

可能：

* 极小值
* 极大值
* 鞍点（saddle point）

---

### 凸函数中的驻点

如果函数凸。

则驻点一定是全局最优。

这是凸优化最重要性质之一。

---

# 17. 学习建议与复习重点

## 17.1 必须掌握的核心公式

重点记忆：

1. 梯度下降更新公式
2. 二次上界
3. 充分下降引理
4. 凸函数收敛速度
5. 强凸函数线性收敛
6. 条件数定义

---

## 17.2 考试和作业高频点

尤其容易考：

* Armijo条件
* 为什么  $  t<2/L  $ 
* 为什么  $  t=1/L  $  最优
*  $  O(1/k)  $  与线性收敛区别
* 条件数为什么影响速度
* zig-zag现象原因
* 强凸与普通凸区别

---

## 17.3 本节课真正核心思想

老师这节课真正想表达的是：

优化算法的速度。

本质上取决于：

函数几何结构（geometry）。

特别是：

* 曲率（curvature）
* 条件数（condition number）

而后面很多高级算法：

* 牛顿法
* 拟牛顿法
* Adam
* 自适应优化器

本质上都在解决：

"不同方向曲率不一致"

这个问题。

因此本节课是后面所有优化算法的理论基础。
