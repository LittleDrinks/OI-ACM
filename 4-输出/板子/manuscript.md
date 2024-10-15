# 杂项


## 快读

```cpp
inline int read()
{
	int x=0,f=1;char ch=getchar();
	while (ch<'0'||ch>'9'){if (ch=='-') f=-1;ch=getchar();}
	while (ch>='0'&&ch<='9'){x=x*10+ch-48;ch=getchar();}
	return x*f;
}
```


# 计算几何


# 图论


## 最短路


### 有向图上判环


## 最小生成树


## LCA


## 树的直径


## Tarjan


## 匈牙利算法


## 网络流


# 数据结构


## 并查集


## Trie


## ST表

```cpp
const int N=1e5+5, I=20;

struct ST {
	int st[N][I+5], Log[N];
	void init()
	{
		Log[1] = 0;
		for (int i = 2; i <= n; ++i) { Log[i] = Log[i/2] + 1; }
		for (int j = 1; j <= n; ++j) {
			st[j][0] = read();
		}
		for (int i = 1; i <= I; ++i) {
			for (int j = 1; j+(1<<(i-1)) <= n; ++j) {
				st[j][i] = max( st[j][i-1], st[j+(1<<(i-1))][i-1] );
			}
		}
	}
	int query(int l, int r)
	{
		int s = Log[r-l+1];
		return max(st[l][s], st[r-(1<<s)+1][s]);
	}
} st;
```


## 树状数组

单点修改，区间求和
```cpp
const int N=5e5+5;
int n, m;

struct BIT {
	int t[N];
	int lowbit(int x) { return x&-x; }
	void modify(int x, int d)
	{
		for (; x <= n; x += lowbit(x)) { t[x] += d; }
	}
	ll query(int x)
	{
		ll res = 0;
		for (; x; x-=lowbit(x)) { res += t[x]; }
		return res;
	}
} T;
```
区间修改，单点查询
```cpp
const int N=5e5+5;
int n, m;
ll a[N];

struct BIT {
	ll t[N];
	int lowbit(int x) { return x&(-x); }
	void modify(int x, int d)
	{
		for (; x <= n; x += lowbit(x)) { t[x] += d; }
	}
	ll query(int x)
	{
		ll res = 0;
		for (; x; x -= lowbit(x)) { res += t[x]; }
		return res;
	}
} T;
```

## 线段树

区间修改，区间求和
```cpp
const int N=1e5+5;
int n, m;
ll a[N];

struct segment_tree {
	struct node {
		int l, r;
		ll s, tag;
	} t[N<<2];
	void push_up(int pos)
	{
		int lson=pos*2, rson=pos*2+1;
		t[pos].s = t[lson].s + t[rson].s;
	}
	void spread_down(int pos)
	{
		if (t[pos].tag) {
			int lson=pos*2, rson=pos*2+1;
			t[lson].s += (t[lson].r-t[lson].l+1)*t[pos].tag;
			t[lson].tag += t[pos].tag;
			t[rson].s += (t[rson].r-t[rson].l+1)*t[pos].tag;
			t[rson].tag += t[pos].tag;
			t[pos].tag = 0;
		}
	}
	void build(int pos, int l, int r)
	{
		t[pos].l = l;
		t[pos].r = r;
		if (l == r) { t[pos].s=a[l]; return; }
		int mid=(l+r)>>1, lson=pos*2, rson=pos*2+1;
		build(lson, l, mid);
		build(rson, mid+1, r);
		push_up(pos);
	}
	void modify(int pos, int l, int r, ll d)
	{
		if (l<=t[pos].l && t[pos].r<=r) {
			t[pos].s += (t[pos].r-t[pos].l+1) * d;
			t[pos].tag += d;
			return;
		}
		spread_down(pos);
		int mid=(t[pos].l+t[pos].r)>>1, lson=pos*2, rson=pos*2+1;
		if (l <= mid)   { modify(lson, l, r, d); }
		if (mid+1 <= r) { modify(rson, l, r, d); }
		push_up(pos);
	}
	ll query(int pos, int l, int r)
	{
		if (l<=t[pos].l && t[pos].r<=r) { return t[pos].s; }
		spread_down(pos);
		ll res = 0;
		int mid=(t[pos].l+t[pos].r)>>1, lson=pos*2, rson=pos*2+1;
		if (l <= mid)   { res += query(lson, l, r); }
		if (mid+1 <= r) { res += query(rson, l, r); }
		return res;
	} 
} T;
```

# 数学


## 线性筛


# WA
开 `long long`
叉乘的时候检查是否爆 `long long`
`#define int long long`
位运算的时候考虑下 `long long`，比如 `1LL<<i`
上界开小了
多测没清空
`(int)a.size()`，否则在和负数比较的时候会出错
考虑初态，什么操作都不做
