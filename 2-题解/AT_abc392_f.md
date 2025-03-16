---
tags:
  - 题解
  - 树状数组
aliases:
---
## [F - Insert](https://atcoder.jp/contests/abc392/tasks/abc392_f)

从后往前处理排位。数字 $n$ 的位置为 $p_n$，数字 $n-1$ 的位置为剩下位置中的 $p_{n-1}$，以此类推……
记权值数组 $t[i]=0/1$ 表示第 $i$ 个位置已/未被占，“剩下位置中的第 $x$ 个”等价于第一个前缀和等于 $x$ 的位置，用树状数组倍增维护即可。

#### [AC 代码](https://atcoder.jp/contests/abc392/submissions/62752206)

```cpp
#include <bits/stdc++.h>

using namespace std;

struct BIT {
    int n;
    vector<int> t;
    BIT(int n): n(n) { t.assign(n+1, 0); }
    int lowbit(int x) { return x&-x; }
    void modify(int x, int d) {
        for (; x <= n; x+=lowbit(x)) { t[x] += d; }
    }
    int query(int x) {
        int res = 0;
        for (; x; x-=lowbit(x)) { res += t[x]; }
        return res;
    }
    int select(int x) {
        int ans = 0, sum = 0;
        for (int i = ceil(log2(n)); i >= 0; --i) {
            if (ans+(1<<i) <= n && sum + t[ans+(1<<i)] < x) {
                ans += 1<<i;
                sum += t[ans];
            }
        }
        return ans + 1;
    }
};

void solve()
{
	int n; cin >> n;
    vector<int> p(n+1), a(n+1);
    for (int i = 1; i <= n; ++i) { cin >> p[i]; }
    BIT t(n);
    for (int i = 1; i <= n; ++i) { t.modify(i, 1); }
    for (int i = n; i; --i) {
        int pos = t.select(p[i]);
        a[pos] = i;
        t.modify(pos, -1);
    }
    for (int i = 1; i <= n; ++i) { cout << a[i] << " \n"[i==n]; }
}

int main()
{
	cin.tie(nullptr)->sync_with_stdio(false);
	int t = 1;
	while (t--) { solve(); }
}

```
