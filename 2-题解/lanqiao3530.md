---
tags:
  - 题解
  - 数学
  - 生成函数
aliases:
---
## [高塔](https://www.lanqiao.cn/problems/3530/learning/?page=1&first_category_id=1&name=%E9%AB%98%E5%A1%94)

$O(nm^2)$ 的暴力 DP 是显然的。记 $f(i,j)$ 表示前 $i$ 轮用了 $j$ 的能量，则
$$
f(i,j)=\sum_{k=i-1}^{j-1}f(i-1,k)\times (j-k)\times A_i
$$
```cpp
#include <bits/stdc++.h>

using namespace std;
typedef long long ll;
const int MOD=998244353;
void add(ll &x, ll y) { if ((x+=y)>=MOD) { x -= MOD; } }

int main()
{
	cin.tie(nullptr)->sync_with_stdio(false);
	int n; ll m;
	cin >> n >> m;
	vector<ll> a(n+1);
	for (int i = 1; i <= n; ++i) { cin >> a[i]; }
	vector f(n+1, vector<ll>(m+1));
	ll ans = 0;
	f[0][0] = 1;
	for (int i = 1; i <= n; ++i) {
		for (ll j = i; j <= m; ++j) {
			for (ll k = i-1; k < j; ++k) {
				add(f[i][j], (j-k)%MOD*f[i-1][k]%MOD*a[i]%MOD);
			}
			if (j==m || i==n) { add(ans, f[i][j]); }
		}
	}
	cout << ans << "\n";
	return 0;
}
```

---

考虑构造生成函数 $f_i(x)=A_i\sum_{k=1}^\infty kx^k$，指数为消耗的能量，系数为方案数的增量。则 $\prod_{j=1}^if_j(x)$ 中 $x^k$ 的系数即为第 $i$ 轮消耗了 $k$ 能量的方案数。将其化简得到 $f_i(x)=A_i\cdot\dfrac{x}{(1-x)^2}$。
>[!note] 化简
> 指定 $0<|x|<1$，根据几何级数有 $\dfrac{1}{1-x}=\sum_{k=0}^\infty x^k$。
> 两侧同时求导得到 $\dfrac{1}{(1-x)^2}=\sum_{k=1}^\infty kx^{k-1}$，再同乘 $x$ 得到结果

对于前 $n-1$ 轮，我们只需要知道消耗了 $m$ 格能量的方案数，即 $\prod_{j=1}^if_j(x)$ 中 $x^m$ 的系数。
> [!note] 推导
> 求出指定项前的系数，我们依然可以采用对几何级数 $\dfrac{1}{(1-x)}$ 求导然后对比系数的方式。
> $$
> \begin{align}
> &&\dfrac{1}{(1-x)}&=\sum_{k=0}^\infty x^k\\
> &求一阶导&\dfrac{1\times1}{(1-x)^2}&=\sum_{k=1}^\infty kx^{k-1}\\
> &求二阶导&\dfrac{1\times1\times2}{(1-x)^3}&=\sum_{k=2}^\infty k(k-1)x^{k-2}\\
> &求三阶导&\dfrac{1\times1\times2\times3}{(1-x)^3}&=\sum_{k=3}^\infty k(k-1)(k-2)x^{k-3}\\
> &&\cdots\\
> &求n-1阶导&\dfrac{(n-1)!}{(1-x)^n}&=\sum_{k=n-1}^{\infty}\bigg(x^{k-(n-1)}\prod_{i=0}^{n-2}(k-i) \bigg)\\
> \end{align}
> $$
> 对高阶导的结果整理，然后将其拼凑为所求的幂级数形式
> $$
> \begin{align}
> \dfrac{(n-1)!}{(1-x)^n}&=\sum_{k=n-1}^{\infty}\bigg(x^{k-n+1}\dfrac{k!}{(k-n+1)!} \bigg)\\
> \dfrac{1}{(1-x)^n}&=\sum_{k=n-1}^\infty\bigg(x^{k-n+1}{k\choose n-1}\bigg)\\
> \dfrac{1}{(1-x)^{2n}}&=\sum_{k=2n-1}^\infty\bigg(x^{k-2n+1}{k\choose 2n-1}\bigg)\\
> \dfrac{x^i}{(1-x)^{2i}}&=\sum_{k=2i-1}^\infty\bigg(x^{k-i+1}{k\choose 2i-1}\bigg)
> \end{align}
> $$
> 令 $k-i+1=m\Rightarrow k=m+i-1$，则第 $i$ 轮 $x^m$ 的系数为 $\displaystyle {m+i-1\choose 2i-1}\prod_{j=1}^iA_j$

$\displaystyle{m+i-1\choose 2i-1}=\dfrac{(m+i-1)!}{(2i-1)!(m-i)!}$，可以预处理至多 $2n$ 项 $\dfrac{1}{(2i-1)!}$ 和 $\dfrac{(m+i-1)!}{(m-i)!}=\displaystyle\prod_{k=m-i+1}^{m+i-1}(k)$ 求出。
然后第 $n$ 轮需要将 $x^{n},x^{n+1},\cdots,x^m$ 的系数全部累加得到第 $n$ 轮的答案。即 $$\prod_{j=1}^nA_j\times\sum_{k=n}^{m}{k+n-1\choose 2n-1}$$
> [!note] 推导
> 组合数的曲棍球棒定理指出 $$\sum_{k=r}^n{k\choose r}={n+1\choose r+1}$$
> 该式可以这样理解：为算出 ${n+1\choose r+1}$，枚举第 $r+1$ 个数可能选择的位置 $r+1,r+2,\cdots n+1$，剩下 $r$ 个数应该在该位置前被选择，即 ${r\choose r},{r+1\choose r},\cdots,{n\choose r}$，累加得到上式。
> 根据此公式，得到 $$\sum_{k=n}^{m}{k+n-1\choose 2n-1}={n+m\choose 2n}$$

$\displaystyle{n+m\choose 2n}=\dfrac{(n+m)!}{(2n)! (m-n)!}$，可以通过预处理至多 $2n$ 项 $\dfrac{1}{(2n)!}$ 和 $\dfrac{(n+m)!}{(m-n)!}=\displaystyle\prod_{k=m-n+1}^{n+m}k$ 求出。
综上，答案为 $$\prod_{j=1}^nA_j\times{n+m \choose 2n} + \sum_{i=1}^{n-1}\bigg({m+i-1\choose 2i-1}\prod_{j=1}^iA_j\bigg)$$
在计算时注意特判 $m=n$ 的情况。

[***AC 代码***](https://www.lanqiao.cn/problems/3530/submissions/67d4f731d066087cb971a88d/)

```cpp
#include <bits/stdc++.h>

using namespace std;
typedef long long ll;

const int MOD=998244353;
void add(ll &x, ll y) { if ((x+=y)>=MOD) { x -= MOD; } }
ll qpow(ll a, ll b=MOD-2)
{
    ll res = 1;
    for (; b; b>>=1) {
        if (b & 1) { res = res * a % MOD; }
        a = a * a % MOD;
    }
    return res;
}
int main()
{
    cin.tie(nullptr)->sync_with_stdio(false);
    ll n, m;
    cin >> n >> m;
    vector<ll> a(n+1), pre1((n<<1)+1), pre2((n<<1)+2);

    // 求 a[] 的前缀积
    a[0] = 1;
    for (int i = 1; i <= n; ++i) {
        cin >> a[i];
        a[i] = a[i-1] * a[i] % MOD;
    }

    // 求 [1,2n] 的阶乘
    pre1[0] = 1;
    for (ll i = 1; i <= 2*n; ++i) {
        pre1[i] = pre1[i-1] * i % MOD;
    }

    // 求 [m-n,m+n] 的阶乘
    pre2[0] = (m-n)%MOD;
    for (ll i = m-n+1; i <= n+m; ++i) {
        pre2[i-(m-n)] = i % MOD * pre2[i-1-(m-n)] % MOD;
    }
    auto prod = [&](ll L, ll R) {
        assert(m-n+1<=L && L<=R && R<=m+n);
        return pre2[R-(m-n)]*qpow(pre2[L-(m-n)-1])%MOD;
    };
    ll ans = 0;
    // 累加前 n-1 轮 x^m 的系数
    for (int i = 1; i < n; ++i) {
        add(ans, qpow(pre1[2*i-1])*prod(m-i+1, m+i-1)%MOD*a[i]%MOD);
    }
    // 第 n 轮的答案，注意特判 m==n 的情况
    if (m == n) { add(ans, a[n]); }
    else { add(ans, qpow(pre1[2*n])*prod(m-n+1, n+m)%MOD*a[n]%MOD); }
    cout << ans << "\n";
}
```
