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
### Blackjack Example
