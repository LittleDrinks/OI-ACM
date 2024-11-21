---
tags:
  - 题解
aliases:
  - "[ARC187A] Add and Swap"
---
## [[ARC187A] Add and Swap](https://www.luogu.com.cn/problem/AT_arc187_a)

对 $i$ 位置进行两次操作，相当于 $\begin{cases}a_i:=a_i+k\\a_{i+1}:=a_{i+1}+k\end{cases}$，因此当 $n=2$ 时，如果一次操作内无法满足要求，那么输出 `No`。
当 $n\geq3$ 时，总可以对 $i$ 操作若干次，使得 $a_{i-1}\leq a_i$。
唯一的问题在于按这样操作后可能有 $a_{n-1}>a_n$。此时可以通过对 $n-2$ 操作若干次，将 $a_{n-1}$ 放到很大，满足 $a_n+k\leq a_{n-1}$，此时对 $n-1$ 操作就可以满足 $a_{n-1}\leq a_n$，再根据之前的方法调整一下 $a_{n-2}$ 和 $a_{n-1}$ 即可。

#### [AC代码](https://www.luogu.com.cn/record/190102123)

```cpp
#include <bits/stdc++.h>

using namespace std;
typedef long long ll;

const int N=55;
int n, k;
ll a[N];
vector <int> ans;

void saa(int x)
{
    ans.push_back(x);
    a[x+1] += k;
    swap(a[x], a[x+1]);
}

int main()
{
    cin >> n >> k;
    for (int i = 1; i <= n; ++i) { cin >> a[i]; }
    if (n == 2) {
        if (a[1] <= a[2])        { printf("Yes\n0\n"); }
        else if (a[2]+k <= a[1]) { printf("Yes\n1\n1\n"); }
        else                     { printf("No\n"); }
    }
    else {
        for (int i = 2; i < n; ++i) {
            while (a[i-1] > a[i]) { saa(i); saa(i); }
        }
        if (a[n-1] > a[n]) {
            while (a[n]+k > a[n-1]) { saa(n-2); saa(n-2); }
            saa(n-1);
        }
        while (a[n-2] > a[n-1]) { saa(n-1); saa(n-1); }
        printf("Yes\n");
        printf("%d\n", ans.size());
        for (int x: ans) { printf("%d ", x); }
    }
}
```
