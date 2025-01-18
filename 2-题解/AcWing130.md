---
tags:
  - 题解
  - 栈
  - 卡特兰数
aliases:
  - AcWing130. 火车进出栈问题
  - 火车进出栈问题
speed:
---
## [130. 火车进出栈问题](https://www.acwing.com/problem/content/132/)

四种方法：$O(2^n)$ 枚举、$O(n^2)$ 递推、$O(n^2)$ 动态规划、$O(n)$ 数学。
本题需要数学解法。直接求卡特兰数即可。高精度可以用 python 解决。

#### [AC 代码](https://www.acwing.com/problem/content/submission/code_detail/38843847/)

```python
import math
def C(n, k):
    return math.factorial(n)//math.factorial(k)//math.factorial(n-k);
n = int(input())
print(C(2*n, n)//(n+1))
```
