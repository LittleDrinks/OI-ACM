---
tags:
  - 笔记
aliases:
  - "#DP"
---
[[DP 树形DP]]
[[DP 状压DP]]
[[DP 数位DP]]

“填表法 (Pull DP)”：`dp[i][j] = Σ(dp[i-1][...])`
传统的计数 DP、最优解 DP（如背包问题、最长公共子序列）。
 
 “刷表法 (Push DP)”：`dp[i][j] -> update(dp[i+1][...])`
**顺着题目描述的时间线写代码**
