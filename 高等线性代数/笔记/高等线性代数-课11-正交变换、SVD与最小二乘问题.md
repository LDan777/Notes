
# 从正交变换、SVD 到 Moore-Penrose 伪逆与最小二乘问题
（2025/5/25）

## 核心知识脉络与因果链条

```
[研究刚体运动:平移与旋转] 
       │
       ▼
[二维/三维正交变换分型] ────(无法对角化？)───► [两组独立正交基: SVD]
                                                     │
                                                     ▼
[最小二乘问题 / 逼近理论] ◄─── [应用: 求解矛盾方程组] ◄─── [Moore-Penrose 伪逆 A⁺]

```

---

## 第一部分：几何变换、正交矩阵与三维旋转空间

研究几何运动时，通常关注两类变换：**平移**与**旋转** 。

* **平移变换**： $T(\mathbf{x}) = \mathbf{x} + \mathbf{v}_0$ 。平移不属于线性变换。但为了便于研究，我们通过平移把旋转中心移到空间原点，从而将其转化为**单位内积空间上的线性变换**（即正交变换）进行研究 。


* **正交变换（Rigid Motion）**：保持向量内积、长度和夹角不变的线性变换 。其在标准正交基下的表示矩阵为**正交矩阵** $Q$ ，满足 $Q^T Q = I$ ，故其行列式 $\det(Q) = \pm 1$ 。



### 1. 二维正交矩阵的分型与几何意义

设 $Q = \begin{bmatrix} a & b \\ c & d \end{bmatrix}$ 为二维实正交矩阵，由 $Q^T Q = I$ 带来的分量约束：


$$a^2 + c^2 = 1, \quad b^2 + d^2 = 1, \quad ab + cd = 0$$



由此可以解出两类截然不同的正交矩阵 ：

| 类型 | 矩阵形式 | 行列式 $\det(Q)$ | 几何意义 | 备注 |
| --- | --- | --- | --- | --- |
| **第一类** |  $\begin{bmatrix} \cos\theta & -\sin\theta \\ \sin\theta & \cos\theta \end{bmatrix}$  | <br>$+1$  | <br>**纯旋转变换** | 对应复数乘法（无伸缩的单位复数 $e^{i\theta}$ ） 。|
| **第二类** |  $\begin{bmatrix} \cos\theta & \sin\theta \\ \sin\theta & -\cos\theta \end{bmatrix}$  | <br>$-1$ | <br>**镜像变换 / 反射** | 将 $y$ 分量翻转（化学中的手性/镜像异构，无论怎么旋转都无法重合） 。|

### 2. 三维旋转与欧拉角（Euler Angles）的局限性

到三维空间，正交矩阵依然分为 $\det(Q)=1$（旋转）和 $\det(Q)=-1$（镜像）两类 。

* **欧拉角方法**：任何一个三维旋转矩阵（ $\det(Q)=1$ ）都可以通过连续绕三次不同的坐标轴旋转（如先绕 $X$ 轴、再绕 $Z$ 轴、再绕 $X$ 轴）转出。在计算上表现为通过旋转将一列逐步削出零，最终化为分块对角阵 ：



$$Q = R_x(\phi) R_z(\theta) R_x(\psi)$$


但这三个角（欧拉角）具有缺陷，它强制将旋转解构到固定坐标轴上，无法直接刻画“绕任意斜轴旋转”的本质 。



### 3. 三维旋转的本质定理：必存在固定不动的旋转轴

为了摆脱欧拉角的束缚，我们需要寻找旋转的几何不变子空间 。

> 
> **定理**：任意三维实正交矩阵 $Q$ 且 $\det(Q) = 1$ ，必存在一个特征值为 $\lambda = 1$ 。也就是说，三维纯旋转变换必然存在一条**旋转轴**，在这个方向上的向量经过旋转后保持不动 。
> 
> 

**证明细节**：

1. 考虑 $Q$ 的特征多项式 $p(\lambda) = \det(\lambda I - Q)$ 是一个实系数三次多项式 。根据一元三次方程零点定理，它在实数域中至少有一个实根 。


2. 因为 $Q$ 是正交矩阵，保持向量长度不变，故其所有特征值的模长必为 $1$（即 $|\lambda| [cite_start]= 1$ ） 。


3. 它的三个特征值组合只有两种可能：
* 情况一：三个都是实数 。


* 情况二：一个实根 $\lambda_1$ ，一对共轭虚根 $\lambda_2, \bar{\lambda_2}$ 。




4. 不管哪种情况，由于虚根成对出现，其乘积 $\lambda_2 \cdot \bar{\lambda_2} = |\lambda_2|^2 = 1 > 0$ 恒为正 。


5. 矩阵行列式等于所有特征值的乘积：


$$\det(Q) = \lambda_1 \cdot \lambda_2 \cdot \lambda_3 = 1$$


如果存在虚根，则 $\lambda_1 \cdot 1 = 1 \implies \lambda_1 = 1$ 。
如果全是实根，且取值只能为 $\pm 1$ 。乘积为 $+1$ 的组合只能是 $(1, 1, 1)$ 或 $(1, -1, -1)$ 。
**结论**：无论如何，特征值 $\lambda = 1$ 必须存在 。其对应的特征向量 $\mathbf{v}$ 即为**旋转轴**，在旋转中保持恒等不变 。



### 4. Householder 变换（镜像/反射变换）

为了在数值计算中替代昂贵的旋转消元，数字分析学家 Householder 提出了镜像变换（在纯数学中也常称为高斯变换） 。

给定一个单位法向量 $\mathbf{w}$（ $\|\mathbf{w}\|_2 = 1$ ）所垂直的超平面，任何向量 $\mathbf{x}$ 朝该超平面的镜像翻转变换矩阵为：


$$H = I - 2\mathbf{w}\mathbf{w}^T$$


* **特征值分析**： $\mathbf{w}$ 方向上的向量被完全翻转，特征值为 $-1$ ；而垂直于 $\mathbf{w}$ 的超平面内有 $n-1$ 个线性无关的特征向量，它们保持不动，特征值为 $1$ 。


* **行列式**： $\det(H) = (-1)^1 \cdot (1)^{n-1} = -1$ 。


* **用途**：通过精巧构造 $\mathbf{w}$ ，Householder 变换可以像高斯消元法一样，一次性将矩阵某一列的下方元素强行“削成零” ，同时完美保持矩阵的正交性，是 QR 分解和现代数值计算的基石 。



---

## 第二部分：奇异值分解（SVD）与极分解

相似对角化（ $P^{-1}AP = \Lambda$ ）要求算子必须是“自己空间到自己空间”的映射，且对非正规矩阵（有恶心 Jordan 块的矩阵）往往无能为力 。如果我们打破限制，允许**定义域和值域使用两组不同的标准正交基**，那么对于任何长方形矩阵，都能化为完美的对角阵 。

### 1. 奇异值分解（SVD）的经典构造与严密证明

> **奇异值分解定理**：设 $A \in \mathbb{R}^{m \times n}$ 且 $\text{rank}(A) = r$ 。则存在 $m$ 阶正交阵 $U$ 和 $n$ 阶正交阵 $V$ ，使得：
> 
> 
> $$A = U \Sigma V^T$$
> 
> 
> 其中 $\Sigma = \begin{bmatrix} \Sigma_1 & 0 \\ 0 & 0 \end{bmatrix} \in \mathbb{R}^{m \times n}$ ， $\Sigma_1 = \text{diag}(\sigma_1, \sigma_2, \dots, \sigma_r)$ ，且奇异值满足自大到小排列： $\sigma_1 \ge \sigma_2 \ge \dots \ge \sigma_r > 0$ 。
> 
> 

**基于数学归纳法与算子范数的标准证明细节**：
我们对矩阵的维数 $\min(m, n)$ 施加数学归纳法 。

1. **定义第一奇异值与基向量**：
考虑 $A$ 的矩阵二范数（算子范数），定义为单位球上的最大拉伸度 ：



$$\sigma_1 = \|A\|_2 = \max_{\|\mathbf{v}\|=1} \|A\mathbf{v}\|_2$$


由于单位球是紧集，最大值必能取到。设在上式取得最大值的单位向量为 $\mathbf{v}_1$ ，令 $\mathbf{u}_1 = \frac{A\mathbf{v}_1}{\|A\mathbf{v}_1\|_2} = \frac{A\mathbf{v}_1}{\sigma_1}$ ，从而有 $A\mathbf{v}_1 = \sigma_1 \mathbf{u}_1$ 。


2. **扩充为标准正交基**：
将 $\mathbf{u}_1$ 扩充为 $\mathbb{R}^m$ 的标准正交基 $U_1 = [\mathbf{u}_1, \tilde{U}]$；将 $\mathbf{v}_1$ 扩充为 $\mathbb{R}^n$ 的标准正交基 $V_1 = [\mathbf{v}_1, \tilde{V}]$ 。两边施加正交变换 ：



$$A_1 = U_1^T A V_1 = \begin{bmatrix} \mathbf{u}_1^T \\ \tilde{U}^T \end{bmatrix} A \begin{bmatrix} \mathbf{v}_1 & \tilde{V} \end{bmatrix} = \begin{bmatrix} \mathbf{u}_1^T A \mathbf{v}_1 & \mathbf{u}_1^T A \tilde{V} \\ \tilde{U}^T A \mathbf{v}_1 & \tilde{U}^T A \tilde{V} \end{bmatrix}$$


* 根据定义， $\mathbf{u}_1^T A \mathbf{v}_1 = \mathbf{u}_1^T (\sigma_1 \mathbf{u}_1) = \sigma_1$ 。


* 同样 $\tilde{U}^T A \mathbf{v}_1 = \sigma_1 \tilde{U}^T \mathbf{u}_1 = \mathbf{0}$（因为正交） 。
故矩阵可以写成分块形式：



$$A_1 = \begin{bmatrix} \sigma_1 & \mathbf{w}^T \\ \mathbf{0} & A_{n-1} \end{bmatrix}$$







3. **消去上三角横条 $\mathbf{w}^T$（核心技巧：范数单调性矛盾）**：
我们要证明横条 $\mathbf{w}^T$ 必须全为 $\mathbf{0}$ 。构造向量 $\mathbf{x} = \begin{bmatrix} \sigma_1 \\ \mathbf{w} \end{bmatrix}$ ，作用在 $A_1$ 上：



$$\|A_1 \mathbf{x}\|_2 = \left\| \begin{bmatrix} \sigma_1 & \mathbf{w}^T \\ \mathbf{0} & A_{n-1} \end{bmatrix} \begin{bmatrix} \sigma_1 \\ \mathbf{w} \end{bmatrix} \right\|_2 = \left\| \begin{bmatrix} \sigma_1^2 + \|\mathbf{w}\|_2^2 \\ A_{n-1}\mathbf{w} \end{bmatrix} \right\|_2 \ge \sigma_1^2 + \|\mathbf{w}\|_2^2$$



而根据算子范数定义，亦有：

$$\|A_1 \mathbf{x}\|_2 \le \|A_1\|_2 \|\mathbf{x}\|_2 = \sigma_1 \sqrt{\sigma_1^2 + \|\mathbf{w}\|_2^2}$$



两边综合联立：

$$\sigma_1^2 + \|\mathbf{w}\|_2^2 \le \sigma_1 \sqrt{\sigma_1^2 + \|\mathbf{w}\|_2^2} \implies \sqrt{\sigma_1^2 + \|\mathbf{w}\|_2^2} \le \sigma_1$$


这要求 $\|\mathbf{w}\|_2^2 \le 0$ ，所以 $\mathbf{w} = \mathbf{0}$ 必须成立 。


4. **归纳递推**：
此时 $A_1 = \begin{bmatrix} \sigma_1 & \mathbf{0} \\ \mathbf{0} & A_{n-1} \end{bmatrix}$ 已经实现第一步分块对角化 。对维数更低的 $A_{n-1}$ 沿用上述归纳假设，直至长方形矩阵的一条边被完全消耗（退化为单行、单列或标量），归纳基础成立，最终得到全局的 SVD 分解 。



### 2. SVD 与特征值分解（EVD）的深层映射关系

奇异值并不是凭空捏造出来的，它们本质上是高维对称算子的特征值的平方根 ：


$$A = U\Sigma V^T \implies \begin{cases} A A^T = (U\Sigma V^T)(V\Sigma^T U^T) = U(\Sigma\Sigma^T)U^T \\ A^T A = (V\Sigma^T U^T)(U\Sigma V^T) = V(\Sigma^T\Sigma)V^T \end{cases}$$



* $A^T A$ 的特征向量构成**右奇异向量 $V$**（定义域空间的标准正交基） 。


* $A A^T$ 的特征向量构成**左奇异向量 $U$**（值域空间的标准正交基） 。


* 非零特征值的正平方根 $\sqrt{\lambda_i}$ 即为**奇异值 $\sigma_i$** 。



### 3. 大一统：矩阵的四个基本子空间

通过 SVD，我们可以以极度清晰的几何图景直观地解构现代线性代数的核心——**四个基本子空间** 。
设 $U = [U_1, U_2]$ ，其中 $U_1 \in \mathbb{R}^{m \times r}$ 对应非零奇异值；$V = [V_1, V_2]$ ，其中 $V_1 \in \mathbb{R}^{n \times r}$ 。

| 子空间名称 | 符号表示 | 正交基来源 | 几何诠释 |
| --- | --- | --- | --- |
| **值域（列空间）** | $\text{Ran}(A)$ | $U_1$ 的列向量张成 | 矩阵变换后能到达的真实物理空间 。

 |
| **左零空间** | $\text{Ker}(A^T)$ | $U_2$ 的列向量张成 | 与值域完全正交的补空间。 |
| **行空间** | $\text{Ran}(A^T)$ | $V_1$ 的列向量张成 | 所有不被矩阵吞噬、能有效映射的输入源空间。 |
| **核（零空间）** | $\text{Ker}(A)$ | $V_2$ 的列向量张成 | 凡是落入该空间的向量，经过 $A$ 变换后全部坍缩为零。 |

### 4. 极分解（Polar Decomposition）

极分解是复数极坐标形式 $z = r e^{i\theta}$ 在矩阵域的直接推广 。
对于任意方阵 $A$ ，可以分解为：


$$A = Q H$$



* $Q$ 是一个正交矩阵（类比 $e^{i\theta}$ ，负责纯旋转变换） 。


* $H$ 是一个半正定矩阵（类比模长 $r$ ，负责沿正交轴进行伸缩变换），且 $H = \sqrt{A^T A}$ 具有唯一性 。


* **长方形矩阵的推广**：若 $A$ 是长方形满秩矩阵，可通过简化的 SVD 剥离无用冗余信息，同样实现单侧正交阵与半正定阵的极分解 。



---

## 第三部分：Moore-Penrose 伪逆及其四条件

对于不满秩、或者长方形的矩阵，传统的逆矩阵 $A^{-1}$ 并不存在。但我们仍然希望能像求逆一样“能管多少管多少”，把在可逆子空间内的那一块核心信息“反转”回来，其余管不了的（零空间部分）直接置零 。这就催生了 **Moore-Penrose 伪逆 $A^\dagger$**。

### 1. Penrose 四个核心条件（判定伪逆的终极法则）

对于任意矩阵 $A \in \mathbb{R}^{m \times n}$ ，若存在一个矩阵 $X \in \mathbb{R}^{n \times m}$ 满足以下四个 Penrose 方程，则 $X$ 称为 $A$ 的 Moore-Penrose 伪逆，记作 $A^\dagger$ ：

$$\begin{aligned}
\text{(1) } & A X A = A && \text{（弱化逆：保持 } A \text{ 作用的核心几何结构）} \\
\text{(2) } & X A X = X && \text{（自洽性：} A \text{ 同样是 } X \text{ 的弱化逆）} \\
\text{(3) } & (AX)^T = AX && \text{（正交投影：} AX \text{ 是朝向值域 } \text{Ran}(A) \text{ 的对称正交投影矩阵）} \\
\text{(4) } & (XA)^T = XA && \text{（正交投影：} XA \text{ 是朝向行空间 } \text{Ran}(A^T) \text{ 的对称正交投影矩阵）}
[cite_start]\end{aligned}$$



### 2. 伪逆的唯一性证明

> 
> **定理**：满足上述四个条件的矩阵 $X$ 如果存在，必然唯一 。
> 
> 

**严密推导步骤**：
假设存在两个不同的伪逆矩阵 $X$ 和 $Y$ 均满足这四个条件 。我们将通过精妙的条件交叉嵌套，证明 $X = Y$ ：
由条件(2)， $X = XAX$ 。
利用条件(3) 的对称性， $AX = (AX)^T = X^T A^T$ 。


$$\begin{aligned}
X & = X(AX) = X(AX)^T = X X^T A^T \\
& = X X^T (AYA)^T \quad \text{（将 } A \text{ 替换为 Penrose 条件一的 } A = AYA \text{）} \\
& = X X^T A^T Y^T A^T = X (AX)^T (AY)^T \\
& = X (AX) (AY) = X A Y \quad \text{（利用条件一 } XAX=X \text{ 的逆向变形）}
[cite_start]\end{aligned}$$


目前我们证明了 $X = XAY$ 。接下来对 $Y$ 施加完全对称的对偶变换（利用条件(4)）：


$$\begin{aligned}
Y & = YAY = (YA)Y = (YA)^T Y = A^T Y^T Y \\
& = (AXA)^T Y^T Y = A^T X^T A^T Y^T Y = (XA)^T (YA)^T Y \\
& = (XA) (YA) Y = X A Y
[cite_start]\end{aligned}$$


**两边汇合**：


$$X = XAY = Y$$

**结论**：Moore-Penrose 伪逆矩阵具有极其完美的**唯一性** 。

### 3. 通过 SVD 显式构造伪逆

唯一性证完后，通过 SVD 我们可以极其轻松地把伪逆给显式构造出来 。
已知 $A = U \begin{bmatrix} \Sigma_1 & \mathbf{0} \\ \mathbf{0} & \mathbf{0} \end{bmatrix} V^T$ ，其伪逆为：


$$A^\dagger = V \begin{bmatrix} \Sigma_1^{-1} & \mathbf{0} \\ \mathbf{0} & \mathbf{0} \end{bmatrix} U^T$$

构造法则极其简单：把非零奇异值全部取倒数（ $\sigma_i \to 1/\sigma_i$ ），然后将矩阵完全转置过来 。你可以带入 Penrose 四条件，立刻就能完成验证。

### 4. 核心易错点：反序律不成立

在普通可逆矩阵中， $(AB)^{-1} = B^{-1}A^{-1}$ 恒成立。**但在伪逆中，反序律一般不成立！**


$$(AB)^\dagger \neq B^\dagger A^\dagger \quad (\text{一般情况下})$$

只有当 $A$ 的列正交且 $B$ 的行正交等极其苛刻的特殊物理条件下，反序律才能勉强凑齐 Penrose 四条件 。平时推导公式时切勿盲目乱套反序律。

---

## 第四部分：广义逆解线性方程组与最小二乘问题

伪逆的最大工业价值，就在于它无视方程组是否有解，能直接强行给出一个“最不闹事、最完美”的终极近似解 。

### 1. 线性方程组 $Ax = b$ 有精确解的三个等价条件

当我们面对方程组 $Ax = b$ 时，以下三个数学表述完全等价：

1. $\mathbf{b} \in \text{Ran}(A)$（ $\mathbf{b}$ 落在 $A$ 的值域空间内，即有解） 。


2. $\text{rank}([A, \mathbf{b}]) = \text{rank}(A)$（克罗内克-卡佩里定理）。
3. $A A^\dagger \mathbf{b} = \mathbf{b}$ 。


* **几何代数含义**： $A A^\dagger$ 是一个向 $A$ 的值域空间进行正交投影的投影矩阵 。如果 $\mathbf{b}$ 本身就在空间里，那么投影完后必然还是它自己 。





### 2. 彻底解决矛盾方程组：最小二乘问题与最小范数解

如果 $\mathbf{b} \notin \text{Ran}(A)$（如天体轨道观测时由于仪器误差导致的多点矛盾数据），方程组是彻底无解的 。此时我们的策略是不求精确相等，只求误差的平方和最小 ：


$$\min_{\mathbf{x}} \|A\mathbf{x} - \mathbf{b}\|_2^2$$



#### (1) 代数几何推导法（法方程法）

为了让残差向量 $A\mathbf{x} - \mathbf{b}$ 的模长最短，根据高维几何中的垂线定理，残差向量必须垂直于整个 $A$ 掌控的列空间 $\text{Ran}(A)$ 。

也就是说，列空间里的任何向量与残差做内积都必须为 $0$：


$$A^T (A\mathbf{x} - \mathbf{b}) = \mathbf{0} \implies A^T A \mathbf{x} = A^T \mathbf{b}$$

这就是著名的**法方程（Normal Equation）** 。

* 如果 $A$ 是满列秩的（各观测点线性无关）， $A^T A$ 正定可逆，可以直接求出唯一最优解：


$$\mathbf{x} = (A^T A)^{-1} A^T \mathbf{b}$$





* **惊人的一致性**：如果我们此时代入满列秩矩阵的伪逆公式 $A^\dagger = (A^T A)^{-1} A^T$ ，就会发现其结果精确对应：


$$\mathbf{x} = A^\dagger \mathbf{b}$$






#### (2) SVD 与坐标换元推导法（更具普适性，允许矩阵不满秩）

如果矩阵 $A$ 连满秩都不满足（存在无数个解能同时达到最小二乘误差），该如何推导？我们直接带入 SVD 分块求解：
令 $A = U \Sigma V^T$ ，代入最小化目标中，利用正交阵作用不改变二范数模长的性质（二范数正交不变性） ：


$$\|A\mathbf{x} - \mathbf{b}\|_2 = \|U \Sigma V^T \mathbf{x} - \mathbf{b}\|_2 = \|U(\Sigma V^T \mathbf{x} - U^T \mathbf{b})\|_2 = \|\Sigma \mathbf{y} - \mathbf{c}\|_2$$


这里引入变量代换： $\mathbf{y} = V^T \mathbf{x}$ ， $\mathbf{c} = U^T \mathbf{b}$ 。
对方程组进行精确分块拉平：


$$\left\| \begin{bmatrix} \Sigma_1 & \mathbf{0} \\ \mathbf{0} & \mathbf{0} \end{bmatrix} \begin{bmatrix} \mathbf{y}_1 \\ \mathbf{y}_2 \end{bmatrix} - \begin{bmatrix} \mathbf{c}_1 \\ \mathbf{c}_2 \end{bmatrix} \right\|_2^2 = \|\Sigma_1 \mathbf{y}_1 - \mathbf{c}_1\|_2^2 + \|\mathbf{c}_2\|_2^2$$

1. 为了让上式取到最小值，由于 $\mathbf{c}_2$ 是死数据无法改变，我们只能让第一项完全等于 $0$ ，解出：

$$\mathbf{y}_1 = \Sigma_1^{-1} \mathbf{c}_1$$


2. **重点在这里**：对于残差大小， $\mathbf{y}_2$ 取任何值都对误差完全没有任何贡献！为了在无数个可行解中挑选出一个“最干净、最不闹事”的解，我们额外附加一个条件：**要求解向量自身的二范数 $\|\mathbf{x}\|_2 = \|\mathbf{y}\|_2$ 同样最小** 。


3. 观察 $\|\mathbf{y}\|_2^2 = \|\mathbf{y}_1\|_2^2 + \|\mathbf{y}_2\|_2^2$ ，要让它最小，最直接的做法就是让无用的自由变量直接归零： $\mathbf{y}_2 = \mathbf{0}$ ！


4. 于是我们得到唯一确定的终极解向量：

$$\mathbf{y}_{opt} = \begin{bmatrix} \Sigma_1^{-1} \mathbf{c}_1 \\ \mathbf{0} \end{bmatrix} = \begin{bmatrix} \Sigma_1^{-1} & \mathbf{0} \\ \mathbf{0} & \mathbf{0} \end{bmatrix} \begin{bmatrix} \mathbf{c}_1 \\ \mathbf{c}_2 \end{bmatrix} = \begin{bmatrix} \Sigma_1^{-1} & \mathbf{0} \\ \mathbf{0} & \mathbf{0} \end{bmatrix} U^T \mathbf{b}$$


5. 逆向换元恢复出原未知数 $\mathbf{x}$：

$$\mathbf{x}_{opt} = V \mathbf{y}_{opt} = V \begin{bmatrix} \Sigma_1^{-1} & \mathbf{0} \\ \mathbf{0} & \mathbf{0} \end{bmatrix} U^T \mathbf{b} = A^\dagger \mathbf{b}$$



**终极结论**：
无论线性方程组是超定、欠定、满秩还是非满秩，**$\mathbf{x} = A^\dagger \mathbf{b}$ 永远是该方程组在全空间内有且仅有的一个“最小二乘最佳近似且自身范数最小”的完美解** 。这就是伪逆在现代数据科学和工程计算中真正的威力所在。
