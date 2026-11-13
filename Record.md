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

##### 2.偏移量

设 $u$ 为 $v$ 的父亲节点。

记 $(u,v)$ 的偏移量为 $u$ 节点与 $v$ 节点的 DFS 序之差减一，$\varDelta s = dfn[u]-dfn[v]-1$，即表示抵达 $u$ 节点后遍历了其余多少节点后才遍历到 $v$，也就是在 $v$ 前被访问的兄弟节点 $w$ 的子树大小和。

$d[v][i]$ 表示以 $u$ 为根，$u$ 与 $v$ 偏移量为 $i$ 的方案数，不含 $v$ 内部节点方案数。

用 $dp[i][j]$ 辅助转移，表示以 $u$ 为根，已经选择了 $i$ 个子节点遍历，且子树大小和为 $j$。初始状态 $\displaystyle dp[0][0]=\prod_{v \in son(u)}{f[v]}$。

对于所有 $v\in son(u)$

$$
dp[i][j]\gets dp[i][j]+dp[i-1][j-siz[v]]
$$

用退背包去掉 $v$

$$
odp[i][j]\gets dp[i][j]-odp[i-1][j-siz[v]]
$$

计算 $d[v][i]$

$$
d[v][i] = \sum_{j=0}^{\left|son(u)\right|-1}{odp[j][i]}\cdot j!\cdot \left(\left|son(u)-j-1\right|\right)!\cdot f[v]^{-1}
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