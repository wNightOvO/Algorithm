# Record by wNightOvO

## 动态规划

### [P9669 [ICPC 2022 Jinan R] DFS Order 2](https://www.luogu.com.cn/problem/P9669)

提交记录：[R309564587](https://www.luogu.com.cn/record/309564587)。

#### 关键算法
**退背包（可撤销背包）**、复杂树上计数问题。

#### 题目大意
给出一棵 $n(1\le n\le 500)$ 的树，以 $1$ 为根节点遍历整棵树得到 dfs 序，每个节点的子节点可以以任意顺序访问，所以有多种可能的 dfs 序。对于每个节点，求节点 $u$ 在 dfs 序中处于第 $j(1\le j\le n)$ 个位置的 dfs 的数量，对 $998244353$ 取模。

#### 思路一

记 $ans[u][i]$ 为最终答案。$f[u]$ 为以节点 $u$ 为根的子树的 dfs 序的个数，转移式子 $\displaystyle f[u]=\left|son_u\right|!\times \prod_{v\in son_u}{f[v]}$。

$d[u][i]$ 表示从 $fa_u$ 遍历了除了 $u$ 以外的其他子树并一共经过 $i$ 个节点的方案数，则 $\displaystyle ans[u][i]=\sum_{j=1}^{i-1}{ans[fa_u][j]\times d[u][i-j-1]}$，即枚举 $j$，在 dfs 序的第 $j$ 个位置到达 $fa_u$ 的方案数乘上遍历 $fa_u$ 除 $u$ 外其他子树共 $i-j-1$ 个节点的方案数，就得到在第 $i$ 个位置到达 $u$ 的方案数。

由于需要考虑子树之间的排列问题，$d[u][i]$ 无法直接转移。创建一个辅助数组 $dp[i][j]$ 表示$fa_u$ 在排除 $u$ 的情况下选择遍历了 $i$ 个子树并且一共访问了 $j$ 个节点。对于 $fa_u$ 的非 $u$ 子节点 $v$，考虑选或者不选 $v$ 的情况，有转移 $dp[i][j]=dp'[i][j]+dp'[i-1][j-siz_v]\times f[v]$。套用退背包的模版，先不排除任何数求出最终的 $dp$ 数组，对于将要排除的节点 $u$，$dp'[i][j]=dp[i][j]-dp'[i-1][j-siz_v]\times f[v]$。最后 $\displaystyle d[u][i]=\sum_{j=1}^{\left|son_v\right|}{dp'[j][i]\times j!}$。

#### 错因一

上面方法考虑的 $ans[u][i]$ 并不是真正的答案，而是只考虑了 dfs 为 $i$ 时到达节点 $u$ 的方案数，并没有考虑到达 $u$ 后剩余未访问节点的方案数。

#### 思路二

改进思路一，主要在于