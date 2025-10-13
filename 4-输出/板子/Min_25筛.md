用途：定义积性函数 $f(x)$，求解 $\sum_{i = 1} ^ n f(i)$
使用说明：要将$f(x)$多项式拆成单项式，然后分别计算递推函数g
下板题意为：定义积性函数 $f(x)$，且 $f(p ^ k) = p ^ k(p ^ k - 1)$（$p$ 是一个质数）
```cpp
void min_25(int n) {
	const int MOD = 1000000007, inv2 = 500000004, inv3 = 333333336; //inv2、inv3分别表示2和3的逆元，公式需要
	int sq = sqrt(n);
	vector<int>g1(sq * 3), g2(sq * 3); //递推函数,数量依据多项式拆出来的单项式个数
	vector<int>sp1(sq * 3), sp2(sq * 3); //前i项质数的多项式前缀和
	vector<int>w(sq * 3); //离散化存储要处理的数
	vector<int>idx1(sq * 3), idx2(sq * 3);//记录离散化下标
	
	auto getPrime = [&](int n){
			vector <bool> vis(n + 1);
			vector <int> prime;
			prime.push_back(1);
			vis[1] = 1;
			for (int i = 2; i <= n; ++i) {
				if (!vis[i]) { 
					prime.push_back(i); 
					sp1[prime.size() - 1] = (sp1[prime.size() - 2] + i) % MOD;
					sp2[prime.size() - 1] = (sp2[prime.size() - 2] + i * i) % MOD;
				}
				for (auto p : prime) {
					if (p > 1) {
						if (i * p > n) { break; }
						vis[i * p] = 1;
						if (i % p == 0) { break; }
					}
				}
			}
			
			return prime;
	};

	vector<int> p = getPrime(sq);
	function<int(int, int)> S = [&](int x, int y) ->int {
		if (p[y] >= x)return 0;
		int k = (x <= sq ? idx1[x] : idx2[n / x]);
		int ans = (g2[k] - g1[k] + MOD - (sp2[y] - sp1[y]) + MOD) % MOD;//质数贡献
		for (int i = y + 1; i < p.size() && p[i] * p[i] <= x; i++)//合数贡献
		{
			int pe = p[i];
			for (int e = 1; pe <= x; e++, pe = pe * p[i])
			{
				int xx = pe % MOD;
				ans = (ans + xx * (xx - 1) % MOD * (S(x / pe, i) + (e != 1))) % MOD;
			}
		}
		return ans % MOD;
	};
	int d = 0, cnt = 0;
	while (d < n) {
		d++;
		w[++cnt] = n / d;
		int tmp = w[cnt] % MOD;
		g2[cnt] = tmp * (tmp + 1) / 2 % MOD * (2 * tmp + 1) % MOD * inv3 % MOD - 1;//平方和公式，因为p0=1，所以要把1去掉
		g1[cnt] = tmp * (tmp + 1) / 2 % MOD - 1;
		if (n / d <= sq)idx1[n / d] = cnt;
		else idx2[n / (n / d)] = cnt;
		d = n / (n / d);
	}
	
	for (int i = 1; i < p.size();i++) 
		for (int j = 1; j <= cnt && p[i] * p[i] <= w[j]; j++)
		{
			int k = (w[j] / p[i] <= sq ? idx1[w[j] / p[i]] : idx2[n / (w[j] / p[i])]);
			g1[j] -= p[i] * (g1[k] - sp1[i - 1] + MOD) % MOD;
			g2[j] -= p[i] * p[i] % MOD * (g2[k] - sp2[i - 1] + MOD) % MOD;
			g1[j] %= MOD, g2[j] %= MOD;
			if (g1[j] < 0)g1[j] += MOD;
			if (g2[j] < 0)g2[j] += MOD;
		}
	cout << (S(n, 0) + 1) % MOD;
}
```

也可以用于求非积性函数的质数项前缀和，只需最后计算S的时候去掉合数的贡献即可，例如：Min_25筛求区间素数个数(即n以内素数个数)
```cpp
int min_25(int n) {
	int sq = sqrt(n);
	vector<int>g1(sq * 3);
	vector<int>sp1(sq * 3);
	vector<int>w(sq * 3); 
	vector<int>idx1(sq * 3), idx2(sq * 3);
	auto getPrime = [&](int n) {
		vector <bool> vis(n + 1);
		vector <int> prime;
		prime.push_back(1);
		vis[1] = 1;
		for (int i = 2; i <= n; ++i) {
			if (!vis[i]) {
				prime.push_back(i);
				sp1[prime.size() - 1] = (sp1[prime.size() - 2] + 1);
			}
			for (auto p : prime) {
				if (p > 1) {
					if (i * p > n) { break; }
					vis[i * p] = 1;
					if (i % p == 0) { break; }
				}
			}
		}
		return prime;
	};
	vector<int> p = getPrime(sq);
	function<int(int, int)> S = [&](int x, int y) ->int {
		if (p[y] >= x)return 0;
		int k = (x <= sq ? idx1[x] : idx2[n / x]);
		int ans = g1[k] -sp1[y];
		return ans;
		};
	int d = 0, cnt = 0;
	while (d < n) {
		d++;
		w[++cnt] = n / d;
		int tmp = w[cnt];
		g1[cnt] = tmp - 1;
		if (n / d <= sq)idx1[n / d] = cnt;
		else idx2[n / (n / d)] = cnt;
		d = n / (n / d);
	}

	for (int i = 1; i < p.size(); i++)
		for (int j = 1; j <= cnt && p[i] * p[i] <= w[j]; j++)
		{
			int k = (w[j] / p[i] <= sq ? idx1[w[j] / p[i]] : idx2[n / (w[j] / p[i])]);
			g1[j] -= g1[k] - sp1[i - 1];
		}
	return S(n, 0);
}
```