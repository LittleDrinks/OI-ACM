---
tags:
---
邻接矩阵存图，时间复杂度为 $O\left(\dfrac{n^3}{w}\right)$，可以处理 $2000$ 左右的数据。
```cpp
bitset<N> b[N];
for (int j = 1; j <= n; ++j) {  // 注意中转点在最外层
    for (int i = 1; i <= n; ++i) {
        if (b[i][j]) { b[i] |= b[j]; }
    }
}
```
