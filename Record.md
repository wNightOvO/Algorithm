# Record by wNightOvO

## 动态规划

### [P9669 [ICPC 2022 Jinan R] DFS Order 2](https://www.luogu.com.cn/problem/P9669)

**退背包（可撤销背包）**+复杂树上计数问题。

题目大意：给出一棵 $n(1\le n\le 500)$ 的树，以 $1$ 为根节点遍历整棵树得到 dfs 序，每个节点的子节点可以以任意顺序访问，所以有多种可能的 dfs 序。对于每个节点，求节点 $u$ 在 dfs 序中处于第 $j(1\le j\le n)$ 个位置的 dfs 的数量。