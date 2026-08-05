# Markov Processes
## 定义
**定义**：一个状态为Markov状态，当且仅当
$$
\Pr[S_{t+1}\mid S_t]=\Pr[S_{t+1}\mid S_1,...,S_t]
$$

**State Transition Matrix**：对于一个Markov状态 $s$ 与他的后继状态 $s'$ ，*状态转移概率* 为$\mathcal P_{ss'}=\Pr[S_{t+1}=s'\mid S_t=s]$，**状态转移矩阵**为
$$
\begin{bmatrix}
P_{11} & \cdots & P_{1n}\\
\vdots&&\vdots\\ 
P_{n1} & \cdots & P_{nn}
\end{bmatrix}
$$
其中矩阵每一行和为1（从一个状态去往全部可能的状态的概率之和）

**Markov Process**：
**Markov过程**（Markov链）是一个**无记忆随机过程**，被定义为一个二元组$\left<\mathcal S,\mathcal P\right>$，满足
- $\mathcal S$ 是一个（有限）**状态集合**
- $\mathcal P$ 是一个**状态转移矩阵**

整个过程/系统都可以被良定义
### Example
![[Pasted image 20260805171352.png|474]]
我们令$S_1=\text{Class 1}$
每一条箭头上的数字都是**状态转移概率**。每一个圆都是一个状态，其中Sleep节点（方形）就是最终状态，进入了这个状态整个过程就结束了
# Markov Reward Processes
## 定义
**Markov reward process**是具有values的Markov Chain，被定义为一个四元组$\left<\mathcal S,\mathcal P,\mathcal R,\gamma \right>$，满足
- $\mathcal S$ 是一个有限**状态集合**
- $\mathcal P$ 是一个**状态转移矩阵**
- $\mathcal R$ 是reward function
- $\gamma$ 是**discount factor**，$\gamma\in[0,1]$

**定义**：回报$G_t$是**随机变量**，被定义为：
$$
G_t=\sum_{k\in\mathbb N}\gamma^kR_{t+k+1}
$$
表示**从当前开始所有时间步**上的奖励总和
当然，$k$是可能有上限的，准确来说，$k$的上限是最大的步数

**定义**：MRP的状态价值函数$v(s)$被定义为：
$$
v(s) =\mathbb E[G_t\mid S_t=s]
$$
### Example
![[Pasted image 20260805172248.png|475]]
我们令$\gamma=\frac12$，计算出不同 $\mathcal S$ 的回报$G_1$
![[Pasted image 20260805172937.png]]
把无数个这样的$G_1$进行概率加权就得到了这个状态的状态价值函数

不同的 $\gamma$ 的例子

![[Pasted image 20260805174254.png|516]]
![[Pasted image 20260805174304.png|515]]
![[Pasted image 20260805174316.png|492]]
## Bellman Equation for MRPs
贝尔曼方程把状态价值函数描述为两个部分
$$
\begin{align}
v(s)&=\mathbb E[G_t\mid S_t=s]\\
&=\mathbb E[R_{t+1}+\gamma G_{t+1}\mid S_t=s]\\
&=\mathbb E[R_{t+1}\mid S_t=s]+\mathbb E\left\{\mathbb E[\gamma G_{t+1}\mid S_t=s]\right\}\\
&=\mathbb E[R_{t+1}+\gamma v(S_{t+1})\mid S_t=s]
\end{align}
$$
用树的关系来描述：
![[Pasted image 20260805175134.png|287]]
我们可以得到一个公式：
$$
v(s)=\mathcal R_s+\gamma\sum_{s'\in \mathcal S}\mathcal P_{ss'}v(s')
$$
求和式的意思就是对子节点的值进行概率加权求和（积分）

容易得到贝尔曼方程的矩阵形式
$$
\begin{bmatrix} v(1) \\ \vdots \\ v(n) \end{bmatrix} = \begin{bmatrix} \mathcal{R}_1 \\ \vdots \\ \mathcal{R}_n \end{bmatrix} + \gamma \begin{bmatrix} \mathcal{P}_{11} & \cdots & \mathcal{P}_{1n} \\ \vdots & \ddots & \vdots \\ \mathcal{P}_{n1} & \cdots & \mathcal{P}_{nn} \end{bmatrix} \begin{bmatrix} v(1) \\ \vdots \\ v(n) \end{bmatrix}
$$
### Example
![[Pasted image 20260805175811.png|476]]
# Markov Decision Processes
## 定义
**Markov decision process**是具有decisions的MRP，被定义为一个四元组$\left<\mathcal S,\mathcal A,\mathcal P,\mathcal R,\gamma \right>$，满足
- $\mathcal S$ 是一个有限**状态集合**
- $\mathcal A$ 是一个**有限动作集合**
- $\mathcal P$ 是一个**状态转移矩阵**——$\mathcal P_{ss'}^a=\Pr[S_{t+1}=s'\mid S_t=s,A_t=a]$
- $\mathcal R$ 是reward function——$\mathcal R_s^a=\mathbb E[R_{t+1}\mid S_t=s,A_t=a]$
- $\gamma$ 是discount factor，$\gamma\in[0,1]$
### Example
![[Pasted image 20260805202227.png|446]]
红色的字代表决策——这不涉及概率，这只是模型做的决定。而下面节点的三条路径的0.2, 0.4, 0.4是