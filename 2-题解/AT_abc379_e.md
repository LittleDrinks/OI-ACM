---
tags:
  - 题解
aliases:
---
## [\[ABC379E\] Sum of All Substrings](https://www.luogu.com.cn/problem/AT_abc379_e)

考虑分别计算每一位的贡献。
比如 $\texttt{11223}$，考虑以最后一位 $\texttt{2}$ 结尾的数字，可以是 $\texttt{1122},\texttt{122},\texttt{22},\texttt{2}$，再考虑倒数第二位以 $2$ 结尾的数字，可以是 $\texttt{11223},\texttt{1223},\texttt{223},\texttt{23}$。作为第 $4$ 位的数字，它一共产生了 $2\times4\times11$ 的贡献。
一般地，从左往右第 $i$ 位的数字产生的贡献是：
$$
s_i\times i\times \underbrace{11\cdots1}_{n-i \text{个} 1}
$$
其中，$s_i\times i$ 可以直接处理，而 $\underbrace{11\cdots1}_{n-i \text{个} 1}$ 可以通过从左到右求一次前缀和处理，时间复杂度 $O(n)$。
另外，直接粗暴使用 python 会 RE，详见 [[20241112183800 为什么python在处理大整数时会RE]]

#### [AC代码](https://www.luogu.com.cn/record/188332196)

```cpp
#include <bits/stdc++.h>

using namespace std;
typedef long long ll;

const int N=2e5+5;
int n;
ll s[N];

int main()
{
    cin >> n;
    for (int i = 1; i <= n; ++i) {
        char x; cin >> x; s[i] = x-'0';
    }
    for (int i = 1; i <= n; ++i) {
        s[i] = s[i-1] + i*s[i];
    }
    for (int i = n; i > 1; --i) {
        s[i-1] += s[i]/10;
        s[i] %= 10;
    }
    for (int i = 1; i <= n; ++i) { cout << s[i]; }
}
```