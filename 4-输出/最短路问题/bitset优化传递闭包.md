---
tags:
---
[洛谷 B3611 【模板】传递闭包](https://www.luogu.com.cn/problem/B3611)

判断有向图上任意两点是否连通的问题。由于是有向图，无法使用并查集，只能用 Floyd。

如果两点间最短路径不是 $+\infty$，那么这两点就可以互相到达。

当然，实现的时候 `f[][]` 中只需要存一个 `bool` 即可。

```cpp
for (int k = 1; k <= n; ++k) {
    for (int i = 1; i <= n; ++i) {
        for (int j = 1; j <= n; ++i) {
            f[i][j] |= f[i][k] & f[k][j];
        }
    }
}
```
