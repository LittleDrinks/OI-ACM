# 快读
```cpp
inline int read()
{
	int x=0,f=1;char ch=getchar();
	while (ch<'0'||ch>'9'){if (ch=='-') f=-1;ch=getchar();}
	while (ch>='0'&&ch<='9'){x=x*10+ch-48;ch=getchar();}
	return x*f;
}
```


# 二进制
```cpp
// 计算一个整数的二进制表示中有多少个 1
__builtin_popcountll(i);
std::popcount((unsigned long long)i);  // c++20

// 计算一个整数的二进制表示中最高位的 1
__lg(i);  // i 最多 32 位
```


# 子集枚举
```cpp
for (int i = 1; i < (1 << n); i++) {
	for (int j = i; j; j = (j - 1) & i) {
		// j 遍历了 i 的所有非空子集
	}
}
```


# __int128流重载
```cpp
istream& operator>> (istream& is, __int128 &x)
{
	string s; is>>s;
	bool f=false;
	__int128 ans = 0;
	for (auto c: s) {
		if (c == '-') { f = true; }
		if (isdigit(c)) { ans = ans * 10 + (c-'0'); }
	}
	x = f? -ans: ans;
	return is;
}
ostream& operator<< (ostream& os, __int128 &x)
{
	string ans;
	function<void(__int128)> write = [&](__int128 x){
		if (x < 0) { ans += '-'; x=-x; }
		if (x > 9) { write(x/10); }
		ans += '0'+x%10;
	};
	write(x); return os << ans;
}
```


# 解除python大数限制
```python
import sys
sys.set_int_max_str_digits(100005)
```



# 整数域三分

```cpp
// 整数域三分求单峰函数最大值
int l, r;
while (l + 2 <= r) {
	int w = (r - l) / 3;
	int m1 = l + w; int v1 = f(m1);
	int m2 = r - w; int v2 = f(m2);
	if (v1 <= v2) {
		l = m1 + 1;
	} else {
		r = m2 - 1;
	}
} 
cout << max(f(l), f(r)) << "\n";
```



# 火车头
```cpp
#pragma GCC optimize(3)
#pragma GCC optimize("unroll-loops")
```



# 运行时间

```cpp
auto start = chrono::high_resolution_clock::now();
auto stop = chrono::high_resolution_clock::now();
auto duration = std::chrono::duration_cast<std::chrono::milliseconds>(stop - start).count();
if (duration > 998) { cout << ans << "\n"; exit(0); }
```
