# Record by wNightOvO

## 动态规划

### [P9669 [ICPC 2022 Jinan R] DFS Order 2](https://www.luogu.com.cn/problem/P9669)

提交记录：[R309564587](https://www.luogu.com.cn/record/309564587)。

#### 关键算法
**退背包（可撤销背包）**、复杂树上计数问题。

#### 题目大意
给出一棵 $n(1\le n\le 500)$ 的树，以 $1$ 为根节点遍历整棵树得到 DFS 序，每个节点的子节点可以以任意顺序访问，所以有多种可能的 DFS 序。对于每个节点，求节点 $u$ 在 DFS 序中处于第 $j(1\le j\le n)$ 个位置的 DFS 的数量，对 $998244353$ 取模。

#### 思路

##### 1. 子树信息

$siz[u]$ 为子树大小，$fa[u]$ 为父亲节点，$S(u)$ 为 $u$ 的子节点的集合，$dfn[u]$ 为 $u$ 节点的 DFS 序。

$f[u]$ 为子树 $u$ 内部的 DFS 序数。

$$
f[u]=\left|S(u)\right|!\prod_{v\in S(u)}{f[v]}
$$

##### 2. 偏移量

设 $u$ 为 $v$ 的父亲节点。

记 $(u,v)$ 的偏移量为抵达 $u$ 节点后遍历了其余多少节点后才遍历到 $v$，也就是在 $v$ 前被访问的兄弟节点 $w$ 的子树大小和。

$d[v][i]$ 表示以 $u$ 为根，$u$ 与 $v$ 偏移量为 $i$ 的方案数，不含 $v$ 内部节点方案数。

用 $dp[i][j]$ 辅助转移，表示以 $u$ 为根，已经选择了 $i$ 个子节点遍历，且子树大小和为 $j$。初始状态 $\displaystyle dp[0][0]=\prod_{v \in S(u)}{f[v]}$。

对于所有 $v\in S(u)$

$$
dp[i][j]\gets dp'[i][j]+dp'[i-1][j-siz[v]]
$$

用退背包去掉 $v$

$$
odp[i][j]\gets dp[i][j]-odp[i-1][j-siz[v]]
$$

计算 $d[v][i]$

$$
d[v][i] = \sum_{j=0}^{\left|S(u)\right|-1}{odp[j][i]}\cdot j!\cdot \left(\left|S(u)\right|-j-1\right)!\cdot f[v]^{-1}
$$

##### 3. 自顶向下求 $g$

$g[u][i]$ 为节点 $u$ 且 DFS 序在第 $i$ 位的 DFS 序数，不考虑 $u$ 子树内的 DFS 序数。

初始 $g[1][1]=1$，即节点 $1$ 第一个访问，不考虑子树时方案只有 $1$。

$v$ 为 $u$ 的子节点。

$$
g[v][i] = \sum_{j=1}^{i-1}{g[u][j]\cdot d[v][i-j-1]}
$$

##### 4. 最终答案

$ans[u][i]$ 为最终 DFS 序数。$g[u][i]$ 考虑子树方案后即可求得。

$$
ans[u][i]=g[u][i]\cdot f[u]
$$

#### 复杂度

空间复杂度 $O(n^2)$。

时间复杂度 $O(n^3)$。

#### 易错点

- DFS 序数只考虑节点 $u$ DFS 序为 $i$ 时，从根节点到 $u$ 的 DFS 序数，没有考虑 $u$ 到剩余节点的 DFS 序数。
- 如果 $g[u][i]$ 考虑了子树 DFS 序数，就会导致继承了子树信息多计数。

#### 技巧

- 求总方案数时，拆成“根节点 $\to u$”和“$u\to$ 其他节点”。
- 一个集合排除一个数的背包问题，且运算可逆（常见于背包计数），可以用退背包。

### [P15649 [省选联考 2026] 找寻者](https://www.luogu.com.cn/problem/P15649)

提交记录：[R309893461](https://www.luogu.com.cn/record/309893461)。

#### 关键算法
退背包（可撤销背包）、概率与期望 dp、树上背包。

#### 题目大意

给定一棵以 $1$ 为根，大小为 $n(1\le n\le 5000)$ 的树。在树上自底向上进行随机链剖分，一条重链的长度定义为其包含的节点数量。对于非叶子节点 $u$，递归确定其所有子节点后，设其子节点分别为 $v_1,v_2,\cdots,v_k$，它们各自所在重链长度分别为 $l_1,l_2,\cdots,l_k$，则 $u$ 选择节点 $v_i$ 为其重儿子的概率为 $\displaystyle \frac{l_i}{\sum_{j=1}^k{l_j}}$。

求每个节点 $x$ 到根节点 $1$ 简单路径上经过轻边数量的期望之和，对 $998244353$ 取模。

#### 思路

##### 1. 子树信息

$siz[u]$，$fa[u]$，$S(u)$ 表示节点 $u$ 的子节点的集合，$h[u]$ 表示以 $u$ 为根的子树从 $u$ 到叶子节点的最长路径包含的节点数（包括 $u$ 本身）（即该点的树的高度），$sh[u]$ 为 $u$ 的所有子节点 $v$ 的 $h[v]$ 之和。

##### 2. 转化题目

难以直接统计。考虑把点的贡献转化为边的贡献，对于一条边 $(u,v)$，$u$ 是 $v$ 的父亲节点，设 $P(u,v)$ 为这一条边为重边的概率，则这一条边的贡献为 $\displaystyle \left(1-P\left(u,v\right)\right)\cdot siz[v]$。

##### 3. 设计状态并转移

$p[u][l]$ 为在 $u$ 子树内随机链剖分后，$u$ 所在重链长度恰好为 $l$ 时的概率，$q[u][L]$ 为 $u$ 的所有兄弟节点 $v$ 的重链长度之和为 $L$ 的概率。

朴素做法，对 $q$ 的转移：

对于节点 $u$，设 $k=|S(u)|$，其子节点分别为 $v_1,v_2,\cdots,v_k$，维护 $pre[i][L],suf[i][L]$ 分别表示前 $i$ 个子节点和后 $i$ 个子节点的重链长度的和为 $L$ 的概率，初始值 $pre[0][0]=suf[k+1][0]=1$。

$$
pre[i][L]=\sum_{j=1}^{\min(h[v_i],L)}{pre[i-1][L-j]\cdot p[v_i][j]}\\
suf[i][L]=\sum_{j=1}^{\min(h[v_i],L)}{suf[i+1][L-j]\cdot p[v_i][j]}
$$

对于节点 $v_i$

$$
q[v_i][L]=\sum_{x+y=L}{pre[i-1][x]\cdot suf[i+1][y]}
$$

对于 $p$ 的转移：

$$
p[u][l]=\sum_{v\in S(u)}\left[h[v]\ge l-1\right]{\sum_{L=|S(u)|-1}^{sh[u]-h[v]}{p[v][l-1]\cdot q[v][L]\cdot \frac{l-1}{L+l-1}}}
$$

其中 $[P]$ 是示性函数，$P$ 成立时为 $1$，否则为 $0$。

##### 4. 答案统计

设边集为 $E$。

$$
P(u,v)=\sum_{l=1}^{h[v]}{\sum_{L=|S(u)|-1}^{sh[u]-h[v]}{p[v][l]\cdot q[v][L] \cdot \frac{l}{L+l}}}
$$

最终答案 $ANS$ 为

$$
ANS=\sum_{(u,v)\in E}{\left(1-P(u,v)\right)\cdot siz[v]}
$$

#### 复杂度

空间复杂度 $O(n^2)$。

求 $1\sim n$ 的逆元 $O(n)$。

令 $\displaystyle T(u)=\sum_{\substack{v,w\in S(u)\\ v\ne w}}{h[v]\cdot h[w]}\le \sum_{\substack{v,w\in S(u)\\ v\ne w}}{siz[v]\cdot siz[w]}$，则 $\displaystyle O\left(\sum_{i=1}^n{T(i)}\right)=O(n^2)$。

对于 3 中对 $q$ 的转移，对于所有 $u$ 求出 $pre,suf$ 的总时间复杂度为 $\displaystyle O\left(\sum_{i=1}^{n}{T(i)}\right)=O(n^2)$，求出所有 $q[v][L]$ 的时间复杂度为 $O(n^3)$；对于 $p$ 的转移和最终答案的求解，总时间复杂度均为 $\displaystyle O\left(\sum_{i=1}^n{T(i)}\right)=O(n^2)$。

故总时间复杂度为 $O(n^3)$。

#### 思路

##### 5. 退背包优化

$q[u][L]$ 所统计的对象为 $u$ 的所有兄弟节点，即 $fa[u]$ 的所有除 $u$ 外的子节点，且转移时运算可逆，尝试用退背包优化。

对于节点 $u$，设 $k=|S(u)|$，其子节点分别为 $v_1,v_2,\cdots,v_k$。

对于节点 $u$，定义 $Q[L]$ 为该节点的所有子节点 $v$ 的重链长度之和等于 $L$ 的概率，初始值 $Q[0]=1$。

枚举 $v$

$$
Q[L]\gets\sum_{t=1}^{\min(h[v],L)}{Q'[L-t]\cdot p[v][t]}
$$

退背包，记最小的 $x$ 使得 $p[v_i][x]\ne 0$

$$
oQ[0]=Q[x]\cdot p[v_i][x]^{-1}\\
oQ[L]=\left(Q[L+x]-\sum_{j=\max(0,x+L-h[v_i])}^{L-1}{oQ[j]\cdot p[v_i][x+L-j]}\right)\cdot p[v_i][x]^{-1}
$$

求出 $Q$ 和退背包求出 $oQ$ 本质上是一次多项式乘法和除法。

最后

$$
q[v_i][L]=oQ[L]
$$

其余部分不变。

#### 复杂度

求出对于所有非叶子节点，求出 $Q$ 和 $oQ$ 的时间复杂度均为 $\displaystyle O\left(\sum_{i=1}^n{T(i)}\right)=O(n^2)$。

总时间复杂度为 $O(n^2)$。

#### 易错点

- 考虑贡献时使用转移简单的边贡献而不是点贡献。
- 树上背包复杂度 $\displaystyle W(u)=\sum_{\substack{v,w\in S(u)\\ v\ne w}}{siz[v]\cdot siz[w]},O\left(\sum_{i=1}^n{W(u)}\right)=O(n^2)$，可以看作是两个不同节点只会在它们的 $\operatorname{lca}$ 处被统计一次。
- 求 $pre,suf,Q$ 时需要排除不必要的转移，否则时间复杂度会退化成 $O(n^3)$。

#### 技巧

- 将点贡献转换成边贡献。
- 树上背包复杂度分析：如果两个不同节点只在 $\operatorname{lca}$ 合并一次，则复杂度为 $O(n^2)$，但是注意要卡满上下界。
- 一个集合排除一个数时的问题，可以考虑用前后缀合并信息求，如果为背包问题且运算可逆（如这一题的概率乘法有逆元，可以乘逆元变回去）可以考虑退背包。