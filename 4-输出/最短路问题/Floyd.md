---
tags:
---
Floyd 算法的本质是 dp。

设 $f[k][u][v]$ 表示经过点 $k$ 松弛后 $u\rightarrow v$ 的最短路径权值和。

刚开始:
$$f[0][u][v]=\begin{cases}w_{u,v} & (u、v之间有边) \\ +\infty & (u、v之间没有边)\end{cases}$$

求最短路的过程就是不断松弛的过程，其状态转移方程为：

$$f[k][u][v] = min\{\  f[k-1][u][k]+f[k-1][k][v]\  \}$$

Floyd 需要三层循环，分别枚举中转点、起点、终点，这三层循环的顺序是 Floyd 算法唯一需要注意的地方。

![](https://images.cnblogs.com/cnblogs_com/blogs/799115/galleries/2331325/o_4fdf79cc.png)

如图所示，我们考虑把枚举起点放在最外层循环，第一个枚举到的是 $u$，此时我的最优选择是通过 $k$ 中转，最短路为 $u\rightarrow k\rightarrow v$，由于 $k$ 点出发的最短路尚未更新，此时 $u\rightarrow v$ 的最短路权值为 $101$。而之后从 $u$ 出发的最短路将不再有机会更新，所以权值为 $3$ 的最短路 $u\rightarrow k\rightarrow x\rightarrow v$ 并没有被找到。

枚举终点同理会有问题，因此，枚举中转点 $k$ 应该放在最外层循环。

最后，可以使用滚动数组优化空间。时间复杂度 $O(n^3)$，空间复杂度 $O(n^2)$。

```cpp
for (int k = 1; k <= n; ++k) {
    for (int i = 1; i <= n; ++i) {
        for (int j = 1; j <= n; ++i) {
            f[i][j] = min( f[i][j], f[i][k]+f[k][j] );
        }
    }
}
```
