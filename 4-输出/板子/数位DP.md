
| 状态      | 含义                                                               |
| ------- | ---------------------------------------------------------------- |
| $pos$   | 当前枚举到了第几位，一般从高到低                                                 |
| $limit$ | 当前枚举的第 $pos$ 位是否受到上界的限制，当 $[pos+1, len]$ 上的数都等于 $a[i]$ 时为 $true$ |
| $last$  | 上一位（第 $pos+1$ 位）填写的值                                             |
| $lead0$ | $[pos+1,len]$ 是否都为前导零，当前零不是前导零时才计算零的贡献                           |
| $r$     | 前缀对 $m$ 取模的余数                                                    |
| $st$    | 状压，一般用于维护奇偶性                                                     |
将 $f$ 初始化为 $-1$，使用 `~f` 判断是否访问。
> [!quote] 例题
> 不含前导零且相邻两个数字之差至少为 $2$ 的正整数被称为 windy 数。求 $[a,b]$ 中有多少 windy 数。
```cpp
#include <bits/stdc++.h>
using namespace std;
using ll = long long;
using db = long double;
using i128 = __int128;

const int L=18;
int a[L+5];
ll f[L+5][15];
ll dfs(int p, bool limit, bool lead0, int last)
{
	if (!p) { return 1; }
	auto& now = f[p][last];
	if (!limit && !lead0 && ~now) {
		return now;
	}
	int up = limit ? a[p] : 9;
	ll res = 0;
	for (int i = 0; i <= up; ++i) {
		if (!lead0 && abs(last-i) < 2) { continue; }
		res += dfs(p-1, limit&&(i==up), lead0&&(i==0), i);
	}
	if (!limit && !lead0) {
		now = res;
	}
	return res;
}
ll query(ll x)
{
	int len = 0;
	for (; x; x /= 10) {
		a[++len] = x % 10;
	}
	return dfs(len, true, true, 0);
}

void solve()
{
	ll a, b;
	cin >> a >> b;
	cout << query(b) - query(a-1) << "\n";
}

int main()
{
	ios::sync_with_stdio(0); cin.tie(0); cout.tie(0); 
	memset(f, -1, sizeof(f));  // 不要忘记设为 -1
	int t = 1;
	// cin >> t;
	while (t--) { solve(); }
}
```