---
tags:
  - 题解
aliases:
  - Reverse 2^i
---
## [AT_abc413_e - Reverse 2^i](https://atcoder.jp/contests/abc413/tasks/abc413_e?lang=en)

联想：一个数 $x$ 含有质因子 $2$ 的个数为 $k$，那么 $\mathrm{lowbit}(x)=2^k$，形象地说，每个位置能操作的区间构成一棵线段树。
对于线段树上的一个父节点，我们可以通过分别反转它的左右儿子，再整体反转，实现交换左右儿子序列的效果。想让整个序列字典序最小，只有可能让两个儿子的字典序尽可能小，然后将字典序更小的那个放到前面。
直接递归模拟这个过程即可，时间复杂度 $O(n2^n)$

[***AC 代码***](https://atcoder.jp/contests/abc413/submissions/68257649)

```cpp
#include <bits/stdc++.h>
using namespace std;
using ll = long long;
using db = long double;
using i128 = __int128;

void solve()
{
    int n;
    cin >> n;
    vector<int> a(1<<n);
    for (auto &x: a) { cin >> x; }
    
    function<void(int,int)> solve = [&](int l, int r) {
        if (l == r - 1) { return; }
        int len = (r-l)>>1;
        solve(l, l+len);
        solve(l+len, r);
        if (a[l] > a[l+len]) {
            reverse(a.begin()+l, a.begin()+l+len);
            reverse(a.begin()+l+len, a.begin()+r);
            reverse(a.begin()+l, a.begin()+r);
        }
    };
    solve(0, 1<<n);
    for (auto x: a) { cout << x << " "; }
    cout << "\n";
}

int main()
{
    ios::sync_with_stdio(0); cin.tie(0); cout.tie(0); 
    int t = 1;
    cin >> t;
    while (t--) { solve(); }
}

```
