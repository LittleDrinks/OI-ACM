[TOC]
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

## vector相关

```cpp
// 多维 vector，需要 c++17，c++14 只能老老实实写 vector<vector<int>>
vector a(n+1, vector (n+1, vector<int>(n+1)));

// 去重与离散化
for (int i = 1; i <= n; ++i) { vec.push_back(a[i]); }
sort(vec.begin(), vec.end());
vec.erase(unique(vec.begin(), vec.end()), vec.end());
for (int i = 1; i <= n; ++i) {
	idx[i]=lower_bound(vec.begin(), vec.end(), a[i])-vec.begin()+1;  // 下标从1开始
}

// 前缀和
vector <ll> a(n), s(n);
partial_sum(a.begin(), a.end(), s.begin());

// 填充 1~n，令 a[i]=i
iota(a.begin(), a.end(), 0);
```

# 计算几何

https://blog.csdn.net/qq_45249273/article/details/123798461
https://csacademy.com/app/geometry_widget/
先判断，再计算，精度会好很多

## 基础定义

```cpp
typedef long long ll;
typedef double db;  // 视情况改为 long double
const db eps=1e-8;  // 极端数据下，放大 eps 可能有奇效
const db PI=acos(-1.0);
int sgn(db x) { return fabs(x)<eps? 0: ( x>0? 1: -1 ); }
int sgn(ll x) { return x==0? 0: ( x>0? 1: -1 ); }
```
## 向量

向量旋转 $\begin{bmatrix}\cos\theta & -\sin\theta\\ \sin\theta & \cos\theta\end{bmatrix}\begin{bmatrix}a_x\\a_y\end{bmatrix}=\begin{bmatrix}\cos\theta a_x & -\sin\theta a_y\\ \sin\theta a_x & \cos\theta a_y\end{bmatrix}$
```cpp
// 为方便不写 template，题目中坐标为浮点数时可用 db 代替 int/ll
struct Point {
	int x, y;
    Point(int x=0, int y=0): x(x), y(y) {}
    void in() { cin >> x >> y; }
    void print() { cout << x << " " << y << "\n"; }
	string info(string name) {
		return format("{}({},{})", name, x, y);  // c++20
	}
	Point operator + (const Point &p) const {
        return Point(x+p.x, y+p.y);
    }
    Point operator - (const Point &p) const {
        return Point (x-p.x, y-p.y);
    }
	Point operator * (const db &k) const {
		return Point(k*x, k*y);
	}
	Point operator / (const db &k) const {
		return Point(x/k, y/k);
	}
	ll operator * (const Point &p) const {
	    return x*p.x+y*p.y;
	}
	ll operator ^ (const Point &p) const { // 叉乘，用的时候记得打括号
	    return x*p.y - y*p.x;
	}
	/* 
	bool operator < (const Point &p) const {
		return x<p.x || (x==p.x && y<p.y);
	}
	*/
	int quad() {  // 下半平面 < 原点和 x 轴正半轴 < 上半平面 < x 轴负半轴
	    if (y < 0)       { return 1; }
	    else if (y > 0)  { return 3; }
	    else if (x >= 0) { return 2; }
	    else             { return 4; }
	}
	bool operator < (const Point &p) const {  // 极角排序
	    Point a=*this, b=p;
	    int qa = a.quad(), qb = b.quad();
	    if (qa == qb) {
	        ll cross = a^b;
	        if (cross == 0) { return abs(a.x) < abs(b.x); }
	        return cross > 0;
	    }
	    return qa < qb;
	}
	ll len2() {
	    return (*this)*(*this);
	}
	db len() {  // 不可改 ll
	    return sqrtl(len2());  // hypotl(x, y)
	}
	Point rotate(db ang) {
	    return Point(x*cos(ang)-y*sin(ang), x*sin(ang)+y*cos(ang));
	}
	Point trunc(db l) {
	    return (*this) * (l/len());
	}
};
typedef Point Vector;

// 判断 c 是否在 ab 的逆时针方向
int toLeft(Point a, Point b, Point c)
{
    return ((b-a)^(c-a))>0;
}

// 三角形面积，如需避免浮点数，可以返回二倍面积
db S(Point A, Point B, Point C)
{
    return 0.5*fabs((A-B)^(A-C));
};

// 二维向量夹角
db getAngle(Vector a, Vector b)
{
    return fabs(atan2(fabs(a^b), a*b));
}

// 余弦定理求第三边
db getThirdSide(Vector a, Vector b)
{
    return sqrtl(a.len2() + b.len2() - a*b*2);
}
```

## 点线操作

点 $P$ 在直线 $AB$ 上，则 $\overrightarrow{AB}\times\overrightarrow{AP}=\vec{0}$，若要求在线段/射线上，再判断 $x,y$ 是否分别位于 $AB$ 之间即可。
判断两线段是否相交：先做两次跨立实验判断是否规范相交，再特判至少三点共线的情况
点 $P$ 到直线 $(A,\vec{v})$ 距离：$\dfrac{\|\vec{v}\times\overrightarrow{AP}\|}{\|\vec{v}\|}$，投影点 $B$ 满足 $\vec{OB}=\vec{OA}+\dfrac{\vec{v}\cdot\overrightarrow{AB}}{\vec{v}^2}\vec{v}$
两直线 $(P_1,\vec{v_1}),(P_2,\vec{v_2})$ 的交点 $Q$ 满足：$\overrightarrow{OQ}=\overrightarrow{OP_1}+\dfrac{\|\vec{v_2}\times\overrightarrow{P_2P_1}\|}{\|\vec{v_1}\times\vec{v_2}\|}\vec{v_1}$

```cpp
// 线段，射线，直线三合一
struct Line {
    Point a, b;
    Vector v;
	Line(Point a, Point b): a(a), b(b) {  // 注意此处用起点和终点构建
        v = b - a;
    }
	// 判断 c 是否在射线的逆时针方向，注意不要写 bool
	int toLeft(Point p) {
	    return sgn(v^(p-a));
	}
    // 判断点 P 是否在线段上，包含端点
	bool onSegment(Point p) {
        return (toLeft(p)==0) && ((p-a)*(p-b)<=0);  // <= 包括端点，< 不包括端点
    }
    // 点 P 到直线 AB 的距离
	db distToLine(Point p) {
	    return fabs((v^(p-a))/v.len());
	}
	// 用整数操作判断直线与点 C 的距离是否小于（等于）某个值 r
	// 常见于判断直线与圆的位置关系
	// 注意叉乘再相乘可能会爆 ll，所以这里用 __int128
	bool disLess(Point p, __int128 r) {
	    __int128 xmult = (v^(p-a));
	    return xmult*xmult >= r*r*v.len2();
	}
};

// 判断两条线段是否相交
bool hasIntersection(Line L1, Line L2)
{
    if (L1.onSegment(L2.a) || L1.onSegment(L2.b) || L2.onSegment(L1.a) || L2.onSegment(L1.b)) {
        return true;
    }
    bool crossStanding = & {  // 跨立实验，lambda 表达式需要 c++17
        return L.toLeft(a)*L.toLeft(b) == -1;
    }
    // 线段端点分布在直线两侧，“线段与线段/线段与直线/直线与直线”是否相交对应修改即可
    return crossStanding(L1, L2.a, L2.b) && crossStanding(L2, L1.a, L2.a);
}

// 求两直线交点
// 求之前应该先判断有没有交点，避免精度误差，同时还要特判共线的情况
Point getIntersection(Line L1, Line L2)
{
    Point P1=L1.a, P2=L2.a;
    Vector v1=L1.v, v2=L2.v;
    return P1 + v1 * ( (v2^(P1-P2)) / (v1^v2) );
}

//点P到线段AB的距离公式
double DistanceToSegment(Point P, Point A, Point B) {
    if(A==B) return Length(P-A);
    Vector v1=B-A, v2=P-A, v3=P-B;
    if(dcmp(Dot(v1,v2))<0) return Length(v2);
    if(dcmp(Dot(v1,v3))>0) return Length(v3);
    return DistanceToLine(P,A,B);
}

//点P在直线AB上的投影点
Point GetLineProjection(Point P, Point A, Point B) {
    Vector v=B-A;
    return A+v*(Dot(v,P-A)/Dot(v,v));
}
```

## 多边形

多边形的面积 $S=\dfrac12\|\sum_{i=0}^{n-1}\overrightarrow{OP_i}\times\overrightarrow{OP_{(i+1)\mod n}}\|$
```cpp
struct Polygon {
    vector <Point> poly;
    int n;
    // 将一个顺时针的凸包变成逆时针
    void counterClockwise() {
        bool f = false;
        for (int i = 0; i < n; ++i) {
            if (Line(poly[i], poly[(i+1)%n]).toLeft(poly[(i+2)%n]) == -1) { f=true; }
        }
        if (f) { reverse(poly.begin(), poly.end()); }
    }
    Polygon(int n): n(n) {
        poly.resize(n);
        for (auto &p: poly) { p.in(); }
        counterClockwise();  // 顺时针凸包才需要
    }
    Polygon(vector<int>poly): poly(poly) {
    	n = poly.size();
    }
    // 求多边形面积
    // 为避免精度误差，可以返回 2*S，即去掉 "0.5*()"
    db S() {
        db res = 0;
        for (int i = 0; i < n; ++i) {
            res += 0.5*(poly[i]^poly[(i+1)%n]);
        }
        return fabs(res);
    }
    // Sunday's algorithm 光线回转法
    // 在整数范围内判断一个点是否在多边形（不保证凸包）内部
    // 亦可用于求某点相对于该多边形的回转数
	int inPolygon(Point p) {
	    int wn = 0;
	    for (int i = 0; i < n; ++i) {
	        if (Line(poly[i], poly[(i + 1) % n]).onSegment(p)) { return -inf; }
	        int k = Line(poly[i], poly[(i+1)%n]).toLeft(p);
	        int d1 = sgn(poly[i].y - p.y);
	        int d2 = sgn(poly[(i + 1) % n].y - p.y);
	        if (k>0 && d1<=0 && d2>0) { wn++; }
	        if (k<0 && d2<=0 && d1>0) { wn--; }
	    }
	    // return wn;
	    return wn!=0;
	}
};
```

```cpp
// 判断点是否在凸包内部
{   //点在凸包边上
    bool onSeg = false;
    for (int j = 0; j < n; ++j) {
        if (onSegment(poly[j], poly[(j+1)%3], {a,b})) {
            onSeg = true;
        }
    }
    if (onSeg) { cout << "YES\n"; continue; }
}
{   // 点在凸包内部
    int cnt = 0;
    for (int j = 0; j < n; ++j) {
        cnt += toLeft(poly[j], poly[(j+1)%3], Point(a,b));
    }
    if (abs(cnt) == n) { cout << "YES\n"; }
    else               { cout << "NO\n"; }
}
```

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

```cpp
struct Dsu {
    int n;
    vector <int> fa;
    vector <ll> cnt;
    Dsu(int n): n(n) {
        fa.resize(n+1);
        iota(fa.begin(), fa.end(), 0);
        cnt.resize(n+1, 1);
    }
    int find(int x) {
        return fa[x]==x? fa[x]: fa[x]=find(fa[x]);
    }
    void merge(int x, int y) {
        x = find(x);
        y = find(y);
        fa[y] = x;
        cnt[x] += cnt[y];
    }
    void add(int x) {
        ++cnt[find(x)];
    }
};
Dsu D(n);
```

## Trie

01 Trie
```cpp
struct Trie {
	int tot=0, ch[N*I][2];
	void insert(int x)
	{
		int now = 0;
		for (int i = I; i >= 0; --i) {
			int b = (x>>i)&1;
			if (!ch[now][b]) { ch[now][b] = ++tot; }
			now = ch[now][b];
		}
	}
	ll query(int x)
	{
		int now=0;
		ll ans=0;
		for (int i = I; i >= 0; --i) {
			int b = x>>i&1;
			if (ch[now][b^1]) {
				ans = ans*2+1;
				now = ch[now][b^1];
			} else {
				ans = ans*2;
				now = ch[now][b];
			}
		}
		return ans;
	}
} T;
```
## ST表

```cpp
struct ST {
	int n, I=0;
	vector<vector<int>> st;
	vector <int> Log;
	ST(int n, vector<int> a): n(n) {
        while ((1<<I) < n) { ++I; }
		Log.resize(n+1);
		Log[1] = 0;
		for (int i = 2; i <= n; ++i) { Log[i] = Log[i/2] + 1; }
		st.resize(n+1, vector<int>(I+5, 0));
		for (int j = 1; j <= n; ++j) { st[j][0]=a[j]; }
		for (int i = 1; i <= I; ++i) {
			for (int j = 1; j+(1<<(i-1))<=n; ++j) {
				st[j][i] = max(st[j][i-1], st[j+(1<<(i-1))][i-1]);
			}
		}
	}
	int query(int l, int r)
	{
		int s = Log[r-l+1];
		return max(st[l][s], st[r-(1<<s)+1][s]);
	}
};
```

## 对顶multiset

```cpp
struct PairingMultiset {
    int sz;
    multiset <int> minH, maxH;
    ll minS, maxS;
    void clear()
    {
        minH.clear(); maxH.clear();
        sz = minS = maxS = 0;
    }
    void move()
    {
        while ((int)minH.size() < (sz+1)/2) {
            minS += *maxH.rbegin();
            minH.insert(*maxH.rbegin());
            maxS -= *maxH.rbegin();
            maxH.erase(maxH.find(*maxH.rbegin()));
        }
        while ((int)minH.size() > (sz+1)/2) {
            maxS += *minH.begin();
            maxH.insert(*minH.begin());
            minS -= *minH.begin();
            minH.erase(minH.find(*minH.begin()));
        }
    }
    void add(int x)
    {  // 向对顶堆中插入一个元素
        ++sz;
        if (minH.empty() || x >= *minH.begin()) {
            minS += x;
            minH.insert(x);
        } else {
            maxS += x;
            maxH.insert(x);
        }
        move();
    }
    void del(int x)
    {  // 从对顶堆中删除一个x
        --sz;
        if (x >= *minH.begin()) {
            minS -= x;
            minH.erase(minH.find(x));
        } else {
            maxS -= x;
            maxH.erase(maxH.find(x));
        }
        move();
    }
    ll med()
    {  // 求中位数
        return *minH.begin();
    }
    ll dis_to_med()
    {  // 求对顶堆中的数字到中位数的距离和
        ll minSZ=(sz+1)/2, maxSZ=sz-minSZ, m=med();
        return (minS-minSZ*m)+(maxSZ*m-maxS);
    }
} p;
```

## 树状数组

```cpp
struct BIT {
	int n;
    vector <int> t;
    BIT(int n): n(n) { t.resize(n+1, 0); }  // 注意值域树状数组中 n=tot
    int lowbit(int x) { return x&-x; }
    void modify(int x, int d)
    {
        for (; x <= n; x += lowbit(x)) {
            t[x] += d;
        }
    }
    int sum(int x)
    {   // 前缀和
        int res = 0;
        for (; x; x -= lowbit(x)) { res += t[x]; }
        return res;
    }
    int query(int x)
    {   // 求最小的前缀和等于x的位置
        ++x;
        int ans=0, sum=0;
        for (int i = I; i >= 0; --i) {
            if (ans+(1<<i) <= n && sum+t[ans+(1<<i)] < x) {
                ans += (1<<i);
                sum += t[ans];
            }
        }
        return ans + 1;
    }
};
BIT T(n);
```

## 线段树

```cpp
struct lazyTag {
	lazyTag() { }
    lazyTag operator + (const lazyTag &tag) const { }
};
struct info {
    int l, r;
    info(int l=INT_MAX, int r=0): l(l), r(r) { }
    bool operator <= (const pair<int,int> &p) const {
        return p.first <= l && r <= p.second;
    }
    info operator + (const info &f) const { }
    info operator + (const lazyTag &tag) const { }
};
struct segmentTree {
    #define lson (pos << 1) 
    #define rson (pos << 1 | 1)
    struct node {
        info val;
        lazyTag tag;
    };
    int n;
    vector <int> p;
    vector <node> t;
    segmentTree(int n): n(n) {
        p.resize(n+1);
        for (int i = 1; i <= n; ++i) { cin >> p[i]; }
        t.resize(n<<2);
        build(1, 1, n);
    }
    void push_up(int pos) {
        t[pos].val = t[lson].val + t[rson].val;
    }
    void setTag(int pos, const lazyTag &tag) {
        t[pos].val = t[pos].val + tag;
        t[pos].tag = t[pos].tag + tag;
    }
    void spread_down(int pos) {
        t[lson].val = t[lson].val + t[pos].tag;
        t[lson].tag = t[lson].tag + t[pos].tag;
        t[rson].val = t[rson].val + t[pos].tag;
        t[rson].tag = t[rson].tag + t[pos].tag;
        t[pos].tag = lazyTag();
    }
    void build(int pos, int l, int r) {
        if (l == r) {
            t[pos].val = info(p[l], l, r);
        } else {
            int mid = (l + r) >> 1;
            build(lson, l, mid);
            build(rson, mid+1, r);
            push_up(pos);
        }
    }
    void modify(int pos, int l, int r, const lazyTag &tag) {
        if (t[pos].val <= make_pair(l, r)) {
            setTag(pos, tag);
        } else {
            int mid = (t[pos].val.l + t[pos].val.r) >> 1;
            spread_down(pos);
            if (l <= mid) { modify(lson, l, r, tag); }
            if (mid < r)  { modify(rson, l, r, tag); }
            push_up(pos);
        }
    }
    info query(int pos, int l, int r) {
        if (t[pos].val <= make_pair(l, r)) {
            return t[pos].val;
        } else {
            int mid = (t[pos].val.l + t[pos].val.r) >> 1;
            spread_down(pos);
            info res = info();
            if (l <= mid) { res = res + query(lson, l, r); }
            if (mid < r)  { res = res + query(rson, l, r); }
            return res;
        }
    }
};
```
# 数学

## 线性筛

```cpp
vector <int> getPrime(int n)
{
	vector <bool> vis(n+1);
	vector <int> prime;
	vis[1] = 1;
	for (int i = 2; i <= n; ++i) {
		if (!vis[i]) { prime.push_back(i); }
		for (auto p: prime) {
			if (i*p >= n) { break; }
			vis[i*p] = 1;
			if (i%p == 0) { break; }
		}
	}
	return prime;
}
```

## 快速幂与乘法逆元

```cpp
const int MOD=998244353;
ll qpow(ll a, ll b)
{
    ll res=1;
    for (; b; b>>=1) {
        if (b & 1) { res=(res*a)%MOD; }
        a = a*a%MOD;  // 不要少 MOD
    }
    return res;
}
ll Inv(ll x)
{
    return qpow(x, MOD-2, MOD);
}
```

# 烂掉啦
- 开 `long long`
	- `#define int long long`
	- 叉乘的结果再相乘，需要检查是否爆 `long long`
	- 位运算的时候考虑下 `long long`，比如 `1LL<<i`
	- （zxp 于 2024.10.29 没开 `long long` 导致凸包求错 debug 2h，望周知）
- 取 min 时 `inf` 需要开得足够大，如 `9e18`；但如果涉及两个 `inf` 相加减就不能开太极限，如 `1e18`
- 写计算几何的时候，注意返回值和参数的数据类型，是 `int` 还是 `double`
- 上界开小了
	- 写值域相关的数据结构时，数组按值域开而不是按个数开
	- （网络流！）建图的时候考虑清楚点边的意义和数量（如：有可能需要开 $3N$）
- 多测没清空
	- 测 `assert` 时注意全局变量是否清空！！
- `(int)a.size()`，否则在和负数比较的时候会出错
- `cin` 读入大数据导致 `TLE`，总输入量达到 `4e6` 就要快读或者优化 `cin`
- 结构体数组赋初值用花括号而不是圆括号
- 答案要取模
- 写拍子
- 考虑初态，什么操作都不做
- 有两维，可以尝试交换枚举顺序
- 【与】和【或】不要搞反
