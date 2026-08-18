Model-Free 的价值在于——未知的环境&过于庞大的已知环境。
Model-Free Control 分为：
- On-Policy：不断地学习按**自己策略采样**的数据，并更新**这个策略**
- Off-Policy：不断地学习按**其他策略采样**的数据，并更新**自己的策略**
# On-Policy Monte-Carlo Control
## Generalised Policy Iteration
继续使用MC——上一讲的prediction和greedy policy
但是greedy policy是需要model的：
$$
\pi(s)=\operatorname* { argmax }_{a\in\cal A}(\mathcal R_s^a+\mathcal P_{ss'}^aV(s') )
$$
我们不知道状态转移概率

于是我们使用动作价值函数：（本质上是把状态转移概率压到Q里面了，然后通过记录满足另外一个变量——$a$）
$$
\pi(s)=\operatorname* { argmax }_{a\in\cal A} Q(s,a)
$$
![[Pasted image 20260817123114.png|386]]
## Exploration
### ε-Greedy Exploration
由于Model-Free，加上算法本身误差，以及一个并不合适的$\pi$，如果使用贪心策略，很可能**无法到达一些很好的未探索区域**，我们使用一种简单的方法来增加好奇心：
一共$m$种动作，有$1-\varepsilon$的概率使用贪心策略，有$\varepsilon$的概率随机移动
$$
\pi'(a\mid s)=
\begin{cases} \frac{\varepsilon}m+1-\varepsilon, & \text{if } a^*=\underset{a\in A}{\arg\max}\ Q_*(s,a)\\ \frac{\varepsilon}m, & \text{otherwise} \end{cases}
$$
### Monte-Carlo Control
![[Pasted image 20260817162617.png|446]]
如果按照之前的那种更新，我们需要Evaluate多次才能收敛到最优结果。实际上我们不需要跑完所有的episode再用他来训练，我们可以每跑一个episode就用来训练，这样我们可以获得更好的evaluate

但是，我们的策略不能始终是“具有探索性”的，因为**Bellman Optimal Equation是确定的，不应该存在随机的探索策略**。所以我们的策略必须是GLIE
## GLIE
$\varepsilon$-greedy需要满足Greedy in the limit with infinite exploration：
- 所有的$(s,a)$二元组都要被无限次访问，$\lim_{k\to \infty}N_k(s,a)=\infty$
- 最终的策略是贪心的，$\lim_{k\to\infty}\pi_k(a\mid s)=\mathbf1[a=\operatorname*{argmax}_{a'\in\mathcal A}Q_k(s,a')]$

也就是我们要求Agent能探索到所有的状态，并且最终是满足Bellman Optimal Equation
**Example**：当$\varepsilon_k=\frac1k$时，$\varepsilon$-greedy是GLIE
### GLIE Monte-Carlo Control
对于第 $k$ 次迭代：
1. 采样第$k$个episode：$\{S_1,A_1.R_2,...,S_T\}\sim\pi$
2. 更新：$$
   \begin{aligned}
   &N(S_t,A_t)\longleftarrow N(S_t,A_t)+1\\
   &Q(S_t,A_t)\longleftarrow Q(S_t,A_t)+\frac1{N(S_t,A_t)}(G_t-Q(S_t,A_t))\\
   &\varepsilon\longleftarrow\frac1k\\
   &\pi\longleftarrow\varepsilon\text{-greedy}(Q)
   \end{aligned}$$
这会收敛到最优的Q
> [!NOTE] $Q$的初值
> 对于这个算法来说，$Q$是如何初始化的并不重要，因为当第一次探索到某个$(s,a)$的时候，更新过去的式子等于$G_t$，但是对于TD来说并非如此，因为没有计算均值，而是以一个恒定的常数更新
# On-Policy Temporal-Difference Learning
## SARSA($\lambda$)
![[Pasted image 20260817171138.png|117]]
我们有了一个$(s,a)$二元组，然后我们从环境采样，得到我们将获得什么奖励$R$和后继状态$S$
$$
Q ( S, A ) \gets Q ( S, A ) + \alpha \left( R + \gamma Q ( S ^ { \prime }, A ^ { \prime } ) - Q ( S, A ) \right)
$$
