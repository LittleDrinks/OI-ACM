---
tags:
  - 博客
---
# tarjan小结

学了三天tarjan，刷了11道题，现在来总结一下（[戳这里看tarjan知识点](https://cdn.luogu.com.cn/upload/image_hosting/69gw4a3u.png)）。

| 题目地址                                                                                  | AC 代码提交记录                                |
| ------------------------------------------------------------------------------------- | ---------------------------------------- |
| [洛谷P1656 炸铁路](https://www.luogu.com.cn/problem/P1656)                                 | https://www.luogu.com.cn/record/98686211 |
| [洛谷 P7687 [CEOI2005] Critical Network Lines](https://www.luogu.com.cn/problem/P7687 ) | https://www.luogu.com.cn/record/98695821 |
| [洛谷P8436 【模板】边双连通分量](https://www.luogu.com.cn/problem/P8436)                          | https://www.luogu.com.cn/record/98856771 |
| [洛谷P3388 【模板】割点（割顶）](https://www.luogu.com.cn/problem/P3388)                          | https://www.luogu.com.cn/record/98701138 |
| [洛谷 P5058 [ZJOI2004]嗅探器](https://www.luogu.com.cn/problem/P5058 )                     | https://www.luogu.com.cn/record/98711421 |
| [洛谷 P3469 [POI2008]BLO-Blockade](https://www.luogu.com.cn/problem/P3469 )             | https://www.luogu.com.cn/record/98742701 |
| [洛谷P8435 【模板】点双连通分量](https://www.luogu.com.cn/problem/P8435)                          | https://www.luogu.com.cn/record/98852797 |
| [洛谷 P2341 [USACO03FALL / HAOI2006] 受欢迎的牛 G](https://www.luogu.com.cn/problem/P2341 )  | https://www.luogu.com.cn/record/98789076 |

## low思想的应用

| 目标          | 限制  | 判断条件                                 | 额外操作                                                                     |
| ----------- | --- | ------------------------------------ | ------------------------------------------------------------------------ |
| 桥           | 无向图 | `low[v] > dfn[u]`                    | 无                                                                        |
| 边双连通分量（ebc） | 无向图 | `low[v] > dfn[u]`                    | 忽略所有求完的桥，进行 dfs，求得答案                                                     |
| 割点          | 无向图 | `low[v] >= dfn[u]`                   | 根节点需要有 2 个及以上满足条件的子节点，使用 `child` 变量记录并进行特判                               |
| 点双连通分量（pbc） | 无向图 | `low[v] >= dfn[u]`                   | 用栈维护子节点，如果节点 `u` 通过子节点 `v` 判断为割点，</br>那么一路出栈直到 `v` 出栈，最后将 `u` 加入答案中，但不出栈 |
| 强连通分量（scc）  | 有向图 | `low[u]==low[v]`，则 `u` 是这个 scc 的起始位置 | 用栈维护子节点，从栈顶一路删，直到初始位置出栈                                                  |

## 栈优化
在进行图上 dfs 时，同时用栈维护节点，则栈底的点最早被搜索到，栈顶的点最晚被搜索到。
在回溯到节点 `u` 时，从栈顶一路出栈，直到节点 `u`，则可以满足：之前出栈的点，都是节点 `u` 的子节点。
如果设不在栈中的点已经结块，那么通过维护这个栈，可以很快求出节点`u`尚未结块的子节点，从而优化连通分量的标记过程。

## tarjan在题目中的应用
### 所求量是满足特殊条件的桥、割点、scc……
比如：[洛谷 P5058 [ZJOI2004] 嗅探器](https://www.luogu.com.cn/problem/P5058)，就是在割点的基础上，满足删去该点后两个特定节点不连通。一种做法，可以从一个特定节点 `x` 出发开始做 tarjan，这样使得节点 `x` 必然与割点相连。此时只要判定这个点是割点，同时另一个特定节点 `y` 在形成的各连通块中，那么这个点就满足题意。
### 所求量可以在求出桥、割点、scc……的同时顺便求出
比如：[洛谷 P3469 [POI2008]BLO-Blockade](https://www.luogu.com.cn/problem/P3469 )，需要求出割点删去后各连通块的大小。在判断节点 `u` 时需要向下遍历子节点 `v`，此时就可以维护这个连通块的大小，再使用一些数学技巧计算答案。
### 先求 scc、ebc、pbc，然后做缩点，再使用其他算法求解（如树上 dp、拓扑排序等等）
比如：[洛谷 P2341 [USACO03FALL / HAOI2006] 受欢迎的牛 G](https://www.luogu.com.cn/problem/P2341 )，就是先将形成scc的一群奶牛缩点，如果形成森林就是无解，否则出度为0的点所代表的的那群奶牛即为所求。
这类题目一般在图上直接求解比较困难，但在一个 scc、ebc、pbc 中可以无代价转圈以追求更优情况，此时完全可以缩点，去探寻几个连通分量之间如何追求更优情况。

## 一些错了n遍的小细节
1. 存边的数组可能不止 `e[]`；大小不要写 `MAXN`，不要写 `MAXM`，要写 `MAXM<<1`。
2. 缩点后建边是枚举起点向终点建边，因此只需要建单向边，否则会出现重边。
3. 无向图中没有scc，环是ebc。
