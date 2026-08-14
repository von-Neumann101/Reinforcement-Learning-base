Model-Free即Agent无法完全了解环境如何运作——状态转移概率......
# Monte-Carlo Reinforcement Learning
MC 直接从经验中直接学习
## Policy Evaluation
**目标**：从按照$\pi$行动而产生的观察来学习$v_\pi$
$$S_1,A_1,R_2,...,S_k\sim\pi$$

在我们有了一系列的Reward以后，便可以计算$G_t$，进而计算$v_\pi(s)$，不过这里我们使用**经验均值来代替期望**
### First-Visit MC Policy Evaluation
每个回合中，我们只记录第一次到某状态得到的奖励
```text
N[n] <- 0 #每个状态访问的次数
S[n] <- 0 #每个状态的累计回报
V[n] <- 0 #每个状态的价值函数
while k < MAX_ITER do:
	is_terminal <- False
	visited[n] <- False
	while is_terminal do:
		s, G_t, is_terminal = experience();
		if visited[s] = False:
			N[s] <- N[s] + 1
			S[s] <- S[s] + G_t
			V[s] <- S(s) / N[s]
	visited[s] <- False
```
由大数定理，当`N(s)=inf`，那么`V(s)`收敛到$v_\pi(s)$

还有Every-Visit MC Policy Evaluation，即每次到了某个状态都计入，没有`visited[s]`
## Increment Mean
以下**只是一种数学上的重写**，本质和之前的方法没有区别（或者说是为了后续的公式进行一种等价变形）

对于序列$x_1,x_2,...,x_n,...$的均值可以写成如下的递推形式：
$$
\begin{aligned}
\mu _ { k } &= \frac { 1 } { k } \sum _ { j = 1 \atop { \prime } } ^ { k } x _ { j } \\
&= { \frac { 1 } { k } } \left( x _ { k } + \sum _ { j = 1 } ^ { k - 1 } x _ { j } \right) \\
&= \frac { 1 } { k } \left( x _ { k } + ( k - 1 ) \mu _ { k - 1 } \right) \\
&= \mu _ { k - 1 } + \frac { 1 } { k } \left( x _ { k } - \mu _ { k - 1 } \right)
\end{aligned}
$$
### Incremental Monte-Carlo Updates
加入记忆化，就不需要追踪价值的总和了：
$$
v(S_t)\longleftarrow v(S_t)+\alpha(G_t-v(S_t))
$$
$G_t$是实际的回报，我们通过他和我们的预估的价值函数的差值来更新我们的价值函数
# Temporal-Difference Learning
时序差分算法**不需要完整的episode**
## MC and TD
目标和MC一样，都是在给定策略下预测价值函数

我们先考虑$\text{TD}(0)$：
$$
v(S_t)\longleftarrow v(S_t)+\alpha\underbrace{(R_{t+1}+\gamma v(S_{t+1})-v(S_t))}_{\text{TD error: }\delta_t}
$$
和之前类似，MC中我们使用$G_t-v(S_t)$也就是真实值减去 ***当前*** 估计值来更新。但是在TD(0)中，我们对下一步进行估计，也就是往前一步估计$G_t$

比如在开车中，遇到一个即将发生车祸的场景，但是在最后一秒另一个车避开了：
- MC由于没有碰撞，他并不会受到一个大的负奖励
- TD由于很容易预测到接下来会碰撞，它会受到一个很大的负奖励
### Driving Home Example
