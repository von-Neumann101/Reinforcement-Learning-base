# Introduction
Dynamic 指的是具有序列结构或时间结构的问题，Programming 指的是策略（某种需要被优化的东西）
动态规划可以将问题拆解为子问题，通过**解决子问题并将之合并**以得到问题的解法
## Requirements for DP
DP能够解决的问题满足下面的两个性质：
- 最优子结构（必要）：问题分为的**子问题的最优解会描述如何获得问题的最优解**
- 重叠子问题（可无）：子问题的解法会被反复使用（这和效率有关）

MDP就符合上面的两个条件——**Bellman Equation**就描述了一种子问题的分解，并且价值函数存储了子问题的解法（某个状态的最优价值）
## Planning by DP
DP可以：
- prediction：给定一个MDP和策略$\pi$或者一个MRP，DP可以输出任**何状态的价值函数**$v_\pi(s)$
- control：给定一个MDP，输出最优价值函数$v_*$和最优策略$\pi_*$
# Policy Evaluation
## Iterative Policy Evaluation
**问题**：*评估* 一个给定的策略
解法：迭代贝尔曼期望方程
$v_1\to v_2\to\cdots\to v_\pi$（这里的$v$是一个向量，表示MDP所有状态的价值）
使用**同步backup**：对于每次迭代$k+1$，对于每个状态$s\in\mathcal S$，用$v_k(s')$更新$v_{k+1}(s)$
初始的$v_1$为0，最终$v_k$会收敛到$v_\pi$

![[Pasted image 20260807142133.png|326]]
$$
v _ { k + 1 } ( s ) = \sum _ { a \in \mathcal { A } } \pi ( a | s ) \left( \mathcal { R } _ { s } ^ { a } + \gamma \sum _ { s ^ { \prime } \in \mathcal { S } } \mathcal { P } _ { s s ^ { \prime } } ^ { a } v _ { k } ( s ^ { \prime } ) \right)
$$
注意，每一个State都会在一次迭代中作为根节点（顺序没有规定）
### Example: Small Gridworld
![[Pasted image 20260807143419.png|396]]
规则：导致超过边界的动作不会改变State（不会收到奖励），到达灰色块时停止。$\gamma=1$
**Policy**：
$$\pi(n\mid \cdot)=\pi(e\mid \cdot)=\pi(s\mid \cdot)=\pi(w\mid \cdot)=0.25$$
**过程**：
![[Pasted image 20260807151845.png|476]]

![[Pasted image 20260807151854.png|468]]
右边那个grid只是一个贪心策略（其实可以忽略右边的图，这是给下面那一节用的），我们这里进行仅仅是对随机策略$\pi$的$v_\pi$估计
## Improve a Policy
给定一个策略$\pi$，先Evaluate Policy：
$$v_\pi(s)=\mathbb E[R_{t+1}+\gamma R_{t+2}+...\mid S_t=s]$$
然后只需要以贪心策略更新$\pi$即可：
$$
\pi'=\text{greedy}(v_\pi)
$$
无论初始的$v_0,\pi_0$是什么，Policy Iteration的过程**总会使得初始策略$\pi_0$收敛到最佳策略$\pi_*$**，同时也会得到最优价值函数$v_*(s)$
### Example: Car Rental
**规则**：
![[Pasted image 20260807155838.png|564]]

**Policy Iteration**：
![[Pasted image 20260807165252.png|575]]
这里横坐标是第二个位置的车数，纵坐标是第一个位置的车数。图中的数字是调给位置2的车数
### Policy Improvement
证明贪心策略：
$$
\pi ^ { \prime } ( s ) = \underset { a \in \mathcal { A } } { \operatorname { a r g m a x } } \; q _ { \pi } ( s, a )
$$
是最优的

由于
$$
q _ { \pi } ( s, \pi ^ { \prime } ( s ) ) = \operatorname* { m a x } _ { a \in \mathcal { A } } q _ { \pi } ( s, a ) \geq q _ { \pi } ( s, \pi ( s ) ) = v _ { \pi } ( s )
$$
所以
$$
\begin{aligned}
v _ { \pi } ( s ) &\leq q _ { \pi } ( s, \pi ^ { \prime } ( s ) ) = \mathbb { E } _ { \pi ^ { \prime } } \left[ R _ { t + 1 } + \gamma v _ { \pi } ( S _ { t + 1 } ) \mid S _ { t } = s \right] \\
&\leq \mathbb { E } _ { \pi ^ { \prime } } \left[ R _ { t + 1 } + \gamma q _ { \pi } ( S _ { t + 1 }, \pi ^ { \prime } ( S _ { t + 1 } ) ) \mid S _ { t } = s \right] \\
&\leq \mathbb { E } _ { \pi ^ { \prime } } \left[ R _ { t + 1 } + \gamma R _ { t + 2 } + \gamma ^ { 2 } q _ { \pi } ( S _ { t + 2 }, \pi ^ { \prime } ( S _ { t + 2 } ) ) \mid S _ { t } = s \right] \\
&\leq \mathbb { E } _ { \pi ^ { \prime } } \left[ R _ { t + 1 } + \gamma R _ { t + 2 } +... \ | \ S _ { t } = s \right] = v _ { \pi ^ { \prime } } ( s )
\end{aligned}
$$
这里$\mathbb E_{\pi'}$指的是**当前的Action**采样于$\pi'$，那么为什么最后的式子成立呢？
> 因为每次我们都会把$v_\pi$放缩为$q_\pi(s,\pi'(s))$，而这一步需要我们把当前State的Action替换为$\pi'(s)$。可以看到$S_{t+1}$的Action被替换为了$\pi'(S_{t+1})$，我们无限地进行下去，就得到了在未来的每一步都做$\pi'(s)$的Action的价值——这即是$v_{\pi'}(s)$

