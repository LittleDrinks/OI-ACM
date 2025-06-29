```cpp
const int MOD = 1000003;
ll C(ll a, ll b) {
	if (a < b) { return 0; }
	ll down = 1, up = 1;
	for (int i = a, j = 1; j <= b; --i, ++j) {
		up = up * i % MOD;
		down = down * j % MOD;
	}
	return up * qpow(down) % MOD;
}
ll lucas(ll a, ll b) {
	if (a < p && b < p) { return C(a, b); }
	return C(a%MOD, b%MOD) * lucas(a/MOD, b/MOD) % MOD;
}
```