# 杂项



## 常用函数

```cpp
// 快读
inline int read()
{
	int x=0,f=1;char ch=getchar();
	while (ch<'0'||ch>'9'){if (ch=='-') f=-1;ch=getchar();}
	while (ch>='0'&&ch<='9'){x=x*10+ch-48;ch=getchar();}
	return x*f;
}

// 取模
const int MOD=998244353;
void add(ll &x, ll y) { if ((x+=y) >= MOD) { x -= MOD; } }  // 不要漏掉括号
void del(ll &x, ll y) { if ((x-=y) < 0)    { x += MOD; } }
void mul(ll &x, ll y) { (x*=y)%=MOD; }

// 计算一个整数的二进制表示中有多少个 1
__builtin_popcountll(i);
std::popcount((unsigned long long)i);  // c++20

// 计算一个整数的二进制表示中最高位的 1
__lg(i);  // i 最多 32 位
```



## 高精度

重载 `__int128` 输入输出
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



## vector相关

```cpp
// 多维 vector，需要 c++17，c++14 只能老老实实写 vector<vector<int>>
vector a(n+1, vector (n+1, vector<int>(n+1)));

// 最值
int mx = *min_element(vec.begin(), vec.end(), cmp);
int mn = *min_element(vec.begin(), vec.end(), cmp);

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
ll sum = accumulate(s.begin(), s.end(), 0LL);

// 填充 1~n，令 a[i]=i
iota(a.begin(), a.end(), 0);
```



## 随机数

随机数
```cpp
mt19937_64 rng(chrono::steady_clock::now().time_since_epoch().count());
int Rand(int a, int b) { return rng() % (b - a + 1) + a; }
shuffle(a.begin(), a.end(), rng);
```
随机树/图
```cpp
// n 为节点个数
// m 为边的个数，默认为 -1，表示建树
// root 为根节点编号，默认为 -1，
vector<array<int,2>> Graph(int n, int m=-1, int root=-1)
{
	assert(m==-1 || (n-1<=m && m<=1LL*n*(n-1)/2));
	assert(root==-1 || (1<=root && root<=n));
	vector<array<int,2>> E;
	set<array<int,2>> diff;
	// 先建一棵以 0 为根的树，然后选根偏移
	for (int i = 1; i < n; ++i) {
		E.push_back( { i, Rand(0, i-1) } );
	}
	if (root == -1) { root = Rand(0, n-1); }
	for (auto& [x, y]: E) {
		x = (x+root) % n + 1;
		y = (y+root) % n + 1;
		diff.insert( { x, y } );
		diff.insert( { y, x } );  // 无向图中需要添加这条反向边
	}
	// 从树建图
	if (m != -1) {
		for (int i = n-1; i < m; ++i) {
			while (1) {
				int x = Rand(1, n);
				int y = Rand(1, n);
				if (x == y || diff.count({x,y})) { continue; }
				E.push_back( { x, y } );
				diff.insert( { x, y } );
				diff.insert( { y, x } );  // 无向图中需要添加这条反向边
				break;
			}
		}
	}
	shuffle(E.begin(), E.end(), rng);
	return E;
}
```



## 对拍

```cpp
// checker.cpp
while (1) {
	system("gen > data.in");
	system("myc < data.in > myc.out");
	system("std < data.in > std.out");
	if (system("fc my.out std.out")) {  // linux 下换成 diff
		system("pause");
		return 0;
	}
}
```



## bfs

```cpp
const int dx[]={-1, 1, 0, 0}, dy[]={0, 0, -1, 1};  // 上，下，左，右
unordered_map<char,int> d={{'U',0},{'D',1},{'L',2},{'R',3}};

int f[N][N];
bool vis[N][N];
bool valid(int x, int y)
{
    return (
        (1 <= x && x <= h) &&
        (1 <= y && y <= w)
    );
}
int sx, sy, tx, ty;
int bfs()
{
    queue <array<int,3>> q;
    q.push( { sx, sy, 0 } );
    memset(f, 0x3f, sizeof(f));
    while (!q.empty()) {
        auto [x, y, cnt] = q.front(); q.pop();
        if (vis[x][y]) { continue; }
        vis[x][y] = true;
        f[x][y] = cnt;
        for (int dd = 0; dd < 4; ++dd) {
            int nx = x + dx[dd];
            int ny = y + dy[dd];
            if (valid(nx, ny)) { q.push( { nx, ny, cnt+1 } ); }
        }
    }
    if (vis[tx][ty]) { return f[tx][ty]; }
    else { return -1; }
}
```



# 计算几何

https://csacademy.com/app/geometry_widget/
先判断，再计算，精度会好很多
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
template<typename T>
struct point {
	T x, y;
    point(int x=0, int y=0): x(x), y(y) {}
    friend istream& operator >> (istream &is, point &p) { is >> p.x >> p.y; return is; }
	db ang() { return atan2(y, x); }
	bool operator< (const point &p) const { return x<p.x || (x==p.x && y<p.y); }
	point operator+ (const point &p) const { return point(x+p.x, y+p.y); }
    point operator- (const point &p) const { return point(x-p.x, y-p.y); }
	point operator* (const db &k) const { return point(k*x, k*y); }
	point operator/ (const db &k) const { return point(x/k, y/k); }
	T operator* (const point &p) const { return x*p.x + y*p.y; }
	T operator^ (const point &p) const { return x*p.y - y*p.x; }  // 叉乘，用的时候记得打括号
	T len2() { return (*this)*(*this); }
	db len() { return sqrtl(len2()); }  // hypotl(x, y)
	point rotate(db ang) { return point(x*cos(ang)-y*sin(ang), x*sin(ang)+y*cos(ang)); }
	point trunc(db l) { return (*this) * (l/len()); }
};
typedef point<ll> Point;
typedef Point Vector;

// 判断 c 是否在 ab 的逆时针方向
int toLeft(Point a, Point b, Point c) {
    return ((b-a)^(c-a))>0;
}

// 极角排序
// 把全平面划分为 下半平面 < 原点 < x 正半轴 < 上半平面 < x 负半轴
bool argcmp(Point a, Point b) {  
    auto quad = [](const Point &a) {
		if (a.y < 0)       { return 1; }
        else if (a.y > 0)  { return 3; }
        else if (a.x >= 0) { return 2; }
        else               { return 4; }	
    };
    int qa = quad(a), qb = quad(b);
	if (qa == qb) {
	    ll cross = a^b;
	    if (cross == 0) { return abs(a.x) < abs(b.x); }
	    return cross > 0;
	}
	return qa < qb;
}
// 所有点在一个半平面内的简化版本
bool argcmp(Point a, Point b) { return (a^b) > eps; }

// STL 中使用 argcmp()
set<Point,decltype(&argcmp)> evt{&argcmp};
map<Point,pair<ll,ll>,decltype(&argcmp)> evt{&argcmp};
priority_queue<Point,vector<Point>,decltype(&argcmp)> q{&argcmp};

// 三角形面积，如需避免浮点数，可以返回二倍面积
db S(Point A, Point B, Point C) {
    return 0.5*fabs((A-B)^(A-C));
};

// 二维向量夹角
db getAngle(Vector a, Vector b) {
    return fabs(atan2(fabs(a^b), a*b));
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
    bool crossStanding = [&](Line L, Point a, Point b) {  // 跨立实验，lambda 表达式需要 c++17
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



## 凸包与多边形

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



## 圆



# 数据结构



## 带权并查集

```cpp
struct DSU {
    int n;
    vector<int> f, siz, pre;
    DSU(int n): n(n), f(vector<int>(n+1)), siz(vector<int>(n+1,1)), pre(vector<int>(n+1)) {
        iota(f.begin(), f.end(), 0);
    }
    int find(int x) {
        if (f[x] == x) { return x; }
        int fa = find(f[x]);
        pre[x] += pre[f[x]];
        return f[x]=fa;
    }
    bool same(int x, int y) {
        return find(x) == find(y);
    }
    void merge(int x, int y) {
        x = find(x);
        y = find(y);
        f[x] = y;
        pre[x] += siz[y];
        siz[y] += siz[x];
        siz[x] = 0;
    }
    int query(int x, int y) {
        return abs(pre[x]-pre[y])-1;
    }
};
```



## Trie

普通 Trie。
```cpp
const string VAL="abcdefghijklmnopqrstuvwxyzABCDEFGHIJKLMNOPQRSTUVWXYZ0123456789";
const int S=3e6+5, V=62;

struct Trie {
    unordered_map<char,int> p;
    int tot, ch[S][V], pass[S];
    int newNode() {
        ++tot;
        memset(ch[tot], 0, sizeof(ch[tot]));
        pass[tot] = 0;
        return tot;
    }
    void init() {
        tot = -1;
        newNode();
    }
    Trie() {
        for (int i = 0; i < V; ++i) { p[VAL[i]] = i; }
        init();
    }
    void insert(string s) {
        int now = 0;
        for (auto c: s) {
            if (!ch[now][p[c]]) { ch[now][p[c]] = newNode(); }
            now = ch[now][p[c]];
            ++pass[now];
        }
    }
    int query(string s) {
        int now = 0;
        for (auto c: s) {
            if (!ch[now][p[c]]) { return 0; }
            now = ch[now][p[c]];
        }
        return pass[now];
    }
} trie;
```
求 $\displaystyle \max_x \{x\oplus y\}$。求树上最长异或路径。
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

这是一个 $0$ 下标的封装版 ST 表。但是 $1$ 下标时令 $a[0]=0$ 不会影响答案。
```cpp
template <typename T>
struct ST {
    int n=0, I=0;
    vector<int> Log;
    vector<vector<T>> st;
    ST() { }
    ST(const vector<T>& a): n(a.size()), Log(vector<int>(n+1)) {
        for (int i = 2; i <= n; ++i) {
        	I=Log[i]=Log[i/2]+1;
        }
        st.assign(I+1, vector<T>(n));
        copy(a.begin(), a.end(), st[0].begin());
        for (int i = 1; i <= I; ++i) {
            for (int j = 0; j+(1<<(i-1)) < n; ++j) {
                st[i][j] = max( st[i-1][j], st[i-1][j+(1<<(i-1))] );
            }
        }
    }
    T query(int l, int r) {
    	assert(l <= r);  // l > r 时的返回值需要特殊定义
        int s = Log[r-l+1];
        return max( st[s][l], st[s][r-(1<<s)+1] );
    }
    int find(int l, T x) {  // 第一个区间 [l,r] 最值大于等于 x 的 r
    	if (l >= n) { return -1; }
        int rl=l-1, rr=n;
        while (rl != rr-1) {
            int mid = (rl + rr) >> 1;
            if (query(l, mid) >= x) { rr = mid; }
            else { rl = mid; }
        }
        if (rr < n) { return rr; }
        return -1;
    }
};
```



## 对顶multiset维护中位数

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
    void modify(int x, int d) {
        for (; x <= n; x += lowbit(x)) { t[x] += d; }
    }
	// 前缀和
    int query(int x) {
        int res = 0;
        for (; x; x -= lowbit(x)) { res += t[x]; }
        return res;
    }
    // 求最小的前缀和等于x的位置
	int select(int x) {
	    int ans = 0, sum = 0;
	    for (int i = ceil(log2(n)); i >= 0; --i) {
	        if (ans+(1<<i) <= n && sum+t[ans+(1<<i)] < x) { 
	            ans += 1<<i;
	            sum += t[ans];
	        }
	    }
	    return ans + 1;
	}
};
BIT T(n);
```



## 懒标记线段树

懒标记线段树本质上是维护两个幺半群和三个三元操作，对应重载即可。
```cpp
template<typename Info, typename Lazy>
struct segmentTree {
    #define lson (p << 1)
    #define rson (p << 1 | 1)
    #define m ((l+r)>>1)
    int n;
    vector<Info> info;
    vector<Lazy> lazy;
    void push_up(int p) {
        info[p] = info[lson] + info[rson];
    }
    void apply(int p, const Lazy& v) {
        info[p] = info[p] + v;
        lazy[p] = lazy[p] + v;
    }
    void spread_down(int p) {
        apply(lson, lazy[p]);
        apply(rson, lazy[p]);
        lazy[p] = Lazy();
    }
    segmentTree(int n): n(n), info(4 << __lg(n)), lazy(4 << __lg(n)) { }
    segmentTree(const vector<Info> &a): segmentTree(a.size()) {
        function<void(int,int,int)> build = [&](int p, int l, int r){
            if (r - l == 1) { info[p] = a[l]; return; }
            build(lson, l, m);
            build(rson, m, r);
            push_up(p);
        };
        build(1, 0, n);
    }
    void rangeApply(int p, int l, int r, int x, int y, const Lazy& v) {
        if (y <= l || r <= x) { return; }
        if (x <= l && r <= y) { apply(p, v); return; }
        spread_down(p);
        rangeApply(lson, l, m, x, y, v);
        rangeApply(rson, m, r, x, y, v);
        push_up(p);
    }
    Info rangeQuery(int p, int l, int r, int x, int y) {
        if (y <= l || r <= x) { return Info(); }
        if (x <= l && r <= y) { return info[p]; }
        spread_down(p);
        return rangeQuery(lson, l, m, x, y) + rangeQuery(rson, m, r, x, y);
    }
    void modify(int x, int y, ll k) {
    	assert(x <= y);
        rangeApply(1, 0, n, x, y+1, Lazy(k));
    }
    ll query(int x, int y) {
        return rangeQuery(1, 0, n, x, y+1).s;
    }
};
struct Info {
    Info() { }
};
struct Lazy {
    Lazy() { }
};
Info operator+ (const Info &u, const Info &v) {
}
Info operator+ (const Info &u, const Lazy &v) {
    return u;
}
Lazy operator+ (const Lazy &u, const Lazy &v) {
    return u;
}
```


## 分块



## 逆序对

归并排序解决静态逆序对问题。
```cpp
ll msort(vector<int> &a, int l, int r)
{
    if (l == r) { return 0; }
    int mid = (l + r) >> 1;
    ll ans=0;
    ans += msort(a, l, mid);
    ans += msort(a, mid+1, r);
    int i=l, j=mid+1;
    vector<int> b;
    while (i <= mid && j <= r) {
        if (a[i] <= a[j]) { b.push_back(a[i++]); }
        else              { b.push_back(a[j++]); ans+=mid-i+1; }
    }
    while (i <= mid) { b.push_back(a[i++]); }
    while (j <= r)   { b.push_back(a[j++]); }
    copy(b.begin(), b.end(), a.begin()+l);
    return ans;
}
```
树状数组解决静态逆序对问题。
```cpp
ll ans = 0;
for (int i = 0; i < n; ++i) {
    ans += t.query(n)-t.query(p[a[i]]);
    t.modify(p[a[i]], 1);
}
```
分块解决动态逆序对问题。



# 图论

https://csacademy.com/app/graph_editor/
```CPP
typedef pair<int,int> pii;
const int N=2005;
vector <pii> G[N];

void dfs(int u, int fa)
{
    for (auto [v, w]: G[u]) {
        if (v != fa) { dfs(v, u); }
    }
}
```



## 最短路




### bitset优化传递闭包

邻接矩阵存图，时间复杂度为 $O(\dfrac{n^3}{w})$，可以处理 $2000$ 左右的数据。
```cpp
bitset <N> b[N];
for (int j = 1; j <= n; ++j) {  // 注意中转点在最外层
    for (int i = 1; i <= n; ++i) {
        if (b[i][j]) { b[i] |= b[j]; }
    }
}
```



### 有向图上找最小环

枚举图上的一个点 $u$ 作为环的起点，跑一遍 Dijkstra 求出点 $u$ 到剩下所有点 $v$ 的最短距离。如果有一条 $v\to u$ 的边，那么就找到了一个环，环的权值为 $dis[v]+w(v,u)$。最终时间复杂度 $O(NM\log N)$。
```cpp
void Dijkstra(int s)
{
    priority_queue <pii, vector<pii>, greater<pii>> q;
    for (int i = 1; i <= n; ++i) { vis[i]=0; dis[i]=inf; }
    dis[s] = 0;
    q.push( {0, s} );
    while (!q.empty()) {
        int u = q.top().second; q.pop();
        if (vis[u]) { continue; }
        vis[u] = 1;
        for (auto [v,p]: G[u]) {
            if (dis[v] > dis[u] + p) {
                dis[v] = dis[u] + p;
                if (!vis[v]) { q.push( {dis[v], v}); }
            }
            // 与 Dijkstra 唯一的区别
            if (v == s) { mnc = min(mnc, dis[u]+p); }
        }
    }
}
```


### spfa判断负环

```cpp
bool spfa(int s=1)
{
	queue <int> q;
	vector <int> dis(n+1, inf), cnt(n+1, 0);
	vector <bool> inq(n+1, false);
	dis[s] = 0;
	inq[s] = true;
	q.push(s);
	while (!q.empty()) {
		int u = q.front(); q.pop();
        inq[u] = false;
		for (auto& [v, w]: G[u]) {
			if (dis[v] > dis[u] + w) {
				dis[v] = dis[u] + w;
				cnt[v] = cnt[u] + 1;  // 最短路径上的节点数量，无负环则至多为 n-1
				if (cnt[v] >= n) { return true; }
				if (!inq[v]) { q.push(v); inq[v] = true; }
			}
		}
	}
	return false;
}
```


## 基环树上找环

在建图的过程中如果发现 $u,v$ 已经联通，则 $u,v$ 两点必然在环上。从 $u$ 出发 dfs 到 $v$ 即可。
```cpp
vector<int> G[N], cycle;
void dfs(int u, int fa)
{
    cycle.push_back(u);
    if (u == t) {
        sort(cycle.begin(), cycle.end());
        for (int x: cycle) { cout << x << " "; }
        exit(0);
    }
    for (int v: G[u]) {
        if (v != fa) { dfs(v, u); }
    }
    cycle.pop_back();
}
int main()
{
	dsu d(n);  // 见并查集板子
	for (int i = 1; i <= n; ++i) {
	    int u, v;
	    cin >> u >> v;
	    G[u].push_back(v);
	    G[v].push_back(u);
	    if (d.same(u, v)) { s=u; t=v; }
	    d.merge(u, v);
	}
	dfs(s, 0);
}
```


## 最小生成树

kruskal 时间复杂度为 $O(m\log m)$
```cpp
int n, m;
vector<pii> G[N];
int kruskal()
{   // 默认是一张连通图
	vector <array<int,3>> e(m);
	for (auto& [w, u, v]: e) { cin >> u >> v >> w; }
	sort(e.begin(), e.end());
	int ans = 0, cnt = 0;
	d = dsu(n);  // 见并查集模板
	for (auto& [w, u, v]: e) {
		if (d.find(u) != d.find(v)) {
			d.merge(u, v);
			ans += w;
			++cnt;
			// 以下两行为建图，完成后 G 中存放了最小生成树
			G[u].push_back( { v, w } );
			G[v].push_back( { u, w } );
		}
		if (cnt == n-1) { break; }
	}
	return ans;
}
```



## 拓扑排序

```cpp
queue<int> q;
for (int i = 1; i <= n; ++i) {
	if (deg[i] == 0) { q.push(i); }
}
while (!q.empty()) {
	int u = q.front(); q.pop();
	for (int v: G[u]) {
		if (--deg[v] == 0) { q.push(v); }
	}
}
```



## LCA




### 倍增O(LogN)求LCA

```cpp
const int N=5e5+5, I=20;
int n, m, s, dep[N], f[I+5][N], mn[I+5][N];
vector <pii> G[N];

void dfs(int u, int fa)
{   // 预处理父节点、深度、路径上的最小值
    f[0][u] = fa;
    dep[u] = dep[fa] + 1;
    for (int i = 1; i <= I; ++i) {
        f[i][u] = f[i-1][ f[i-1][u] ];
        mn[i][u] = min( mn[i-1][u], mn[i-1][ f[i-1][u] ] );
    }
    for (auto [v, w]: G[u]) {
        if (v != fa) {
            mn[0][v] = w;
            dfs(v, u);
        }
    }
}

int LCA(int u, int v)
{   // 求 u,v 到其 LCA 路径上的最小值
    int ans = numeric_limits<int>::max();
    function<void(int&,int)> jump = [&](int &u, int i) {
        ans = min(ans, mn[i][u]);
        u = f[i][u];
    };
    if (dep[u] < dep[v]) { swap(u, v); }
    for (int i = I; i >= 0; --i) {
        if (dep[f[i][u]] >= dep[v]) { jump(u, i); }
    }
    if (u == v) { return ans; }
    for (int i = I; i >= 0; --i) {
        if (f[i][u] != f[i][v]) {
            jump(u, i); jump(v, i);
        }
    }
    jump(u, 0); jump(v, 0);
    return ans;
}
```



### 欧拉序+ST表O(1)求LCA

初次访问节点 $u$ 和回溯到节点 $u$ 时记录，所产生的序列即为欧拉序。
欧拉序长度为 $2n-1$。
在欧拉序区间 $[first[u],first[v]]$ 上深度最小的节点即为 $lca(u,v)$。
```cpp
// 见 ST 表板子
// 用 pair 实现深度比较，比较函数为 min
template <typename T>
struct ST {};

int first[N], dep[N];
vector <int> G[N];
vector <pii> eular;
void dfs(int u, int fa)
{   // 预处理深度和欧拉序
    first[u] = eular.size();
    dep[u] = dep[fa] + 1;
    eular.push_back( {dep[u], u } );
    for (int v: G[u]) {
        if (v != fa) {
            dfs(v, u);
            eular.push_back( {dep[u], u } );
        }
    }
}

ST<pii> st;
int lca(int u, int v)
{
    u = first[u];
    v = first[v];
    if (u > v) { swap(u, v); }
    return st.query(u, v).second;
}

int main()
{   
	// 预处理欧拉序，构建 ST 表，之后就可以直接调用 lca 进行求解
	dfs(s, 0);
    st = ST<pii>(eular);
}
```



## 树的直径



## 点分治与树的重心

去掉重心所形成的每个连通块的大小都小于等于 $\dfrac{n}{2}$。
```cpp
int n, siz[N], dis[N];
bool vis[N];
vector<pii> G[N];

void calcsiz(int u, int fa, int sum, int &core)
{   // 求树的重心
    siz[u] = 1;
    int mxz = 0;
    for (auto [v, w]: G[u]) {
        if (v != fa && !vis[v]) {
            calcsiz(v, u, sum, core);
            siz[u] += siz[v];
            mxz = max(mxz, siz[v]);
        }
    }
    mxz = max(mxz, sum-siz[u]);
    if (mxz * 2 <= sum) { core = u; }
}

void calcdis(int u, int fa) 
{
    for (auto [v, w]: G[u]) {
        if (v != fa && !vis[v]) {
            dis[v] = dis[u] + w;
            calcdis(v, u);
        }
    }
}

void work(int u, int fa)
{
    vis[u] = true;
    for (auto [v, w]: G[u]) {
        if (v != fa && !vis[v]) {
            dis[v] = w;
            calcdis(v, u);
        }
    }
    for (auto [v, w]: G[u]) {
        if (v != fa && !vis[v]) {
            int sum = siz[v];
            int core = 0;
            calcsiz(v, u, sum, core);
            calcsiz(core, u, sum, core);  // 第二次调用更新 siz[]
            work(core, u);
        }
    }
}

int main()
{
    int core = 0;
    calcsiz(1, 0, n, core);
    calcsiz(core, 0, n, core);
    work(core, 0);
}
```


## Hierholzer求欧拉路

有向图中欧拉通路存在条件：起点出度比入度大 $1$，终点入度比出度大 $1$，其余点入度等于出度。
有向图中欧拉回路存在条件：所有点入度等于出度。
```cpp
void dfs(int u)
{   // Hierholzer 算法
    for (auto [v, id]: G[u]) {
        if (!vis[id]) {
            vis[id] = true;
            dfs(v);
        }
    }
    ans.push_back(u);
}
```


## DSU on tree

求一棵树中有多少子树，满足其中存在的每种颜色的结点个数都相同。
```cpp
const int N=2e5+5;
int n, c[N], siz[N], hvs[N];
int L[N], R[N], dfn[N], totdfn;
vector<int> G[N];

void dfs_init(int u, int fa)
{   // 预处理 dfn 和重儿子
	siz[u] = 1;
	L[u] = ++totdfn;
	dfn[totdfn] = u;
	for (int v: G[u]) {
		if (v != fa) {
			dfs_init(v, u);
			siz[u] += siz[v];
			if (hvs[u] == 0 || siz[v] > siz[hvs[u]]) {
				hvs[u] = v;
			}
		}
	}
	R[u] = totdfn;
}

void add(int u, int dt)
{
	--ccnt[cnt[c[u]]];
	cnt[c[u]] += dt;
	++ccnt[cnt[c[u]]];
}

void dfs_solve(int u, int fa, bool keep)
{
	for (auto v: G[u]) {  // 先做轻儿子，不保留
		if (v != fa && v != hvs[u]) {
			dfs_solve(v, u, false);
		}
	}
	if (hvs[u]) {  // 再做重儿子，保留
		dfs_solve(hvs[u], u, true);
	}
	for (auto v: G[u]) {  // 再把轻儿子的信息加上
		if (v != fa && v != hvs[u]) {
			for (int i = L[v]; i <= R[v]; ++i) {
				add(dfn[i], 1);
			}
		}
	}
	add(u, 1);
	ans += (cnt[c[u]] * ccnt[cnt[c[u]]] == siz[u]);
	if (keep) { return; }
	for (int i = L[u]; i <= R[u]; ++i) {
		add(dfn[i], -1);
	}
}
```



## tarjan


| 定义                               | 限制  | 判断条件                                 | 额外操作                                                                      |
| -------------------------------- | --- | ------------------------------------ | ------------------------------------------------------------------------- |
| 桥：删去这条边后，图上原来连通的两点不再连通           | 无向图 | `low[v] > dfn[u]`                    | 无                                                                         |
| 边双连通分量（EBCC）：任意一条边都不是桥的极大连通子图    | 无向图 | `low[v] > dfn[u]`                    | 忽略所有求完的桥，进行 dfs，求得答案                                                      |
| 割点：删除该点及其边后，图中原来连通的两点不再连通        | 无向图 | `low[v] >= dfn[u]`                   | 根节点需要有 2 个及以上满足条件的子节点，使用 `child` 变量记录并进行特判                                |
| 点双连通分量（PBCC）：任意一个节点都不是割点的极大连通子图  | 无向图 | `low[v] >= dfn[u]`                   | 用栈维护子节点，如果节点 `u` 通过子节点 `v` 判断为割点，  <br>那么一路出栈直到 `v` 出栈，最后将 `u` 加入答案中，但不出栈 |
| 强连通分量（SCC）：有向图中任意两点都可互相到达的极大连通子图 | 有向图 | `low[u]==low[v]`，则 `u` 是这个 SCC 的起始位置 | 用栈维护子节点，从栈顶一路删，直到初始位置出栈                                                   |



### 强连通分量（SCC）

```cpp
struct SCC {
	int n, totdfn=0, colcnt=0;
	stack<int> stk;
	vector<int> dfn, low, col;
	vector<vector<int>> G;
	SCC(int n): n(n) {
		G.assign(n+1, {});
		dfn.assign(n+1, 0);
		low.assign(n+1, 0);
		col.assign(n+1, 0);
	}
	void add_edge(int u, int v) {
		G[u].push_back(v);
	}
	void dfs(int u) {
		stk.push(u);
		dfn[u] = low[u] = ++totdfn;
		for (auto v: G[u]) {
			if (!dfn[v]) {
				dfs(v);
				low[u] = min(low[u], low[v]);
			} else if (!col[v]) {
				low[u] = min(low[u], dfn[v]);
			}
		}
		if (low[u] == dfn[u]) {
			col[u] = ++colcnt;
			int v;
			do {
				v = stk.top(); stk.pop();
				col[v] = colcnt;
			} while(v != u);
		}
	}
	vector<int> work() {
		for (int i = 1; i <= n; ++i) {
			if (!dfn[i]) { dfs(i); }
		}
		return col;
	}
};
```



### 割边与边双连通分量（EBCC）

```cpp
struct EBCC {
    int n, totdfn=0, colcnt=0;
    stack<int> stk;
    vector<pii> B;
    vector<int> dfn, low, col;
    vector<vector<pii>> G;
    EBCC(int n): n(n) {
        G.assign(n+1, {});
        dfn.assign(n+1, 0);
        low.assign(n+1, 0);
        col.assign(n+1, 0);
    }
    void add_edge(int u, int v, int id) {
        G[u].emplace_back(v, id<<1);
        G[v].emplace_back(u, id<<1|1);
    }
    void dfs(int u, int uid) {
        stk.push(u);
        dfn[u] = low[u] = ++totdfn;
        for (auto [v, vid]: G[u]) {
            if ((uid^vid) != 1) {
                if (!dfn[v]) {
                    dfs(v, vid);
                    low[u] = min(low[u], low[v]);
                    if (low[v] > dfn[u]) {  // 求桥
                        B.emplace_back(min(u,v), max(u,v));
                    }
                } else if (!col[v] && dfn[v] < dfn[u]) {
                    low[u] = min(low[u], dfn[v]);
                }
            }
        }
        if (low[u] == dfn[u]) {
            col[u] = ++colcnt;
            int v;
            do {
                v = stk.top(); stk.pop();
                col[v] = colcnt;
            } while (v != u);
        }
    }
    vector<pii> work() {  // 桥
        for (int i = 1; i <= n; ++i) {
            if (!dfn[i]) { dfs(i, 0); }
        }
        return B;
    }
	struct Graph {
	    int n;
	    vector<array<int,2>> E;
	    vector<int> siz, cnt;
	    Graph(int n): n(n) {
	        siz.assign(n+1, 0);  // EBCC 内部点的数量
	        cnt.assign(n+1, 0);  // EBCC 内部边的数量
	    }
	};
	Graph compress() {
	    Graph g(colcnt);
	    for (int i = 1; i <= n; ++i) {
	        ++g.siz[col[i]];
	        for (auto [v, vid]: G[i]) {
	            if (col[v] < col[i]) {
	                g.E.push_back( { col[i], col[v] } );
	            } else if (i < v) {
	                ++g.cnt[col[i]];
	            }
	        }
	    }
	    return g;
	}
};
```


### 割点与点双连通分量（PBCC）



## 最大流

dicnic 在普通图上的时间复杂度是 $O (EV^2)$，在单位容量图上的时间复杂度是 $O (E\min(E^{\frac{1}{2}},V^{\frac{2}{3}}))$
```cpp
const ll INF = 9e18; 
struct Flow {
    vector<tuple<int,ll,int>> G[N];  // 终点，流量，反边
    int dis[N], now[N];  // now[u] 为当前弧
    void add_edge(int u, int v, ll w) {
        G[u].push_back( { v, w, G[v].size() } );
        G[v].push_back( { u, 0, G[u].size()-1 } );
    }
    void bfs(int st) {  // 求层次图
        queue<int> q;
        memset(dis, 0, sizeof(dis));
        q.push(st); dis[st] = 1;
        while (!q.empty()) {
            int u = q.front(); q.pop();
            for (auto& [v, w, inv]: G[u]) {
                if (w != 0 && dis[v] == 0) {
                    dis[v] = dis[u] + 1;
                    q.push(v);
                }
            }
        }
    }
    ll dfs(int u, int t, ll flow) {  // 求阻塞流
        if (u == t) { return flow; }
        for (int &i=now[u]; i < (int)G[u].size(); ++i) {
            auto& [v, w, inv] = G[u][i];
            if (w != 0 && dis[v] > dis[u]) {
                ll d = dfs(v, t, min(flow, w));
                if (d > 0) {
                    w -= d;
                    get<1>(G[v][inv]) += d;
                    return d;
                }
            }
        }
        return 0;
    }
    ll dicnic(int st, int ed) {
        for (ll flow=0, res;;) {
            bfs(st);
            if (dis[ed] == 0) { return flow; }
            memset(now, 0, sizeof(now));
            while ((res=dfs(st,ed,INF)) > 0) {
                flow += res;
            }
        }
    }
};
```



# 数学



## 一些公柿

平方和公式 $\displaystyle\sum_{i=1}^n(i^2)=\dfrac{n(n+1)(2n+1)}{6}$
组合数
① $\displaystyle{n\choose k}=\frac{n!}{k!(n-k)!}$；
② $\displaystyle{n\choose m}={n-1\choose m}+{n-1 \choose m-1}$；
③ $\displaystyle\sum_{k=r}^{n}{n\choose k}={n+1\choose k+1}$；
④ $\displaystyle\sum_{i=0}^n{n\choose i}=2^n$
不定方程 $\displaystyle\sum_{i=1}^mx_i=n$ 正整数解个数为 ${n-1\choose m-1}$，非负整数解的个数为 ${n+m-1\choose m-1}$
几何级数：① $\dfrac{1}{1-x}=\displaystyle\sum_{k=0}^\infty x^k$；② $\dfrac{x}{(1-x)^2}=\displaystyle\sum_{k=1}^\infty kx^k$



## 预处理因数

$[1,n]$ 所有数字的因数个数和是 $O(n\log n)$ 的。
$720720$ 是 $10^6$ 内因数最多的数字，其因数个数为 $240$。
```cpp
vector d(mx+1, vector<int>());
for (int i = 1; i <= mx; ++i) {
    for (int j = i; j <= mx; j += i) {
        d[j].push_back(i);
    }
}
```


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
			if (i*p > n) { break; }
			vis[i*p] = 1;
			if (i%p == 0) { break; }  // 线性筛关键优化
		}
	}
	return prime;
}
```



## 快速幂与乘法逆元

根据费马小定理 $a^{p-1}\equiv1\pmod p$，得 $a^{p-2}\equiv\dfrac{1}{a}\pmod p$，据此计算乘法逆元。
要求模数为质数，且 $a$ 不是 $p$ 的倍数。
```cpp
const int MOD=998244353;
ll qpow(ll a, ll b=MOD-2)
{   // 快速幂函数，x 的乘法逆元为 qpow(x)
    ll res=1;
    for (; b; b>>=1) {
        if (b & 1) { res=(res*a)%MOD; }
        a = a*a%MOD;  // 不要少 MOD
    }
    return res;
}
```



## O(1)计算排列组合数

```cpp
// 预处理阶乘和阶乘的逆元
vector<ll> fac, Inv;
void init(int sz)
{
    fac.resize(sz+1, 1);
    Inv.resize(sz+1, 1);
    for (int i = 1; i <= sz; ++i) {
        fac[i] = fac[i-1] * i % MOD;
        Inv[i] = qpow(fac[i]); 
    }
}
ll C(ll n, ll k) { return n==0? 1: fac[n] * Inv[k] % MOD * Inv[n-k] % MOD; }
ll P(ll n, ll k) { return n==0? 1: fac[n] * Inv[n-k] % MOD; }
```
python 高精度版。
```python
import math
def C(n, k):
    return math.factorial(n)//math.factorial(k)//math.factorial(n-k);
```


## 卡特兰数

$Cat(n)$ 的求法：① $\begin{cases}H_1=1\\H_n=\dfrac{4n-2}{n+1}H_{n-1}\end{cases}$；② $H_n=\dfrac{C_{2n}^n}{n+1}$；③ $H_n=C_{2n}^n-C_{2n}^{n-1}$；④ $H_n=\displaystyle\prod_{i=0}^n H_iH_{n-1-i}$
$Cat(n)$ 的意义：①不跨对角线从 $(1,1)$ 走到 $(n,n)$ 的方案数；② $2n$ 个括号所组成的所有合法括号序列的个数；③合法的出栈方案数；④ $n$ 边形划分为若干个三角形的方案数；⑤ $n+1$ 个节点的二叉树形态。
```cpp
ll Cat(ll n) { return C(2*n, n) * qpow(n+1) % MOD; }
```



## 容斥原理

错位排列问题：$D(n)=(n-1)\times(D(n-1)+D(n-2))$



# 字符串




## 前缀函数与KMP

```cpp
vector<int> prefixFunction(string s)
{
    vector<int> pi(s.length());
    for (int i = 1; i < s.length(); ++i) {
        int j = pi[i-1];
        while (j && s[j]!=s[i]) { j = pi[j-1]; }
        if (s[j] == s[i]) { ++j; }
        pi[i] = j;
    }
    return pi;
}
void kmp()
{
	string s, t;
    cin >> s >> t;
    int n = s.length(), m = t.length();
    vector<int> pi = prefixFunction(t+"#"+s);
    for (int i = m+1; i <= n+m; ++i) {
        if (pi[i] == m) { cout << i-2*m+1 << "\n"; }
    }
    for (int i = 0; i < m; ++i) { cout << pi[i] << " "; }
}
```


## Manacher

```cpp
vector<int> manacher(string s)
{
    vector<int> d(s.length());
    d[0] = 1;
    int r=0, mid=0;
    for (int i = 1; i < s.length()-1; ++i) {  // 最后一个字符为防越界使用，无需访问
        d[i] = 1;
        if (r >= i) { d[i] = min(d[2*mid-i], r-i+1); }
        while (s[i-d[i]] == s[i+d[i]]) { ++d[i]; }  // 需要保证不会越界
        if (i + d[i] > r) { 
            r = i + d[i] - 1;
            mid = i;
        }
    }
    return d;
}
void solve()
{
    string s, t;
    cin >> s;
    for (auto c: s) {
        t.push_back('#');
        t.push_back(c);
    }
    t = "."+t+"#!";  // 首位添加两个不同的字符保证不会越界
    vector<int> d=manacher(t);
    cout << (*max_element(d.begin(), d.end()))-1 << endl;
}
```



## 字符串哈希

```cpp
typedef long long ll;
typedef pair<ll, ll> hs;
const ll MOD1=1e9+7, MOD2=1e9+9;
const hs p = { 117, 131 };
hs operator + (hs a, hs b) {
	return hs{(a.first+b.first)%MOD1, (a.second+b.second)%MOD2};
}
hs operator - (hs a, hs b) {
	return hs{(a.first-b.first+MOD1)%MOD1, (a.second-b.second+MOD2)%MOD2};
}
hs operator * (hs a, hs b) {
	return hs{(a.first*b.first)%MOD1, (a.second*b.second)%MOD2};
}
struct Hash {
    int n;
    vector<hs> hs1, Pow;
    Hash(const string &s) {
        n = s.length();
        hs1.resize(n+2);
        Pow.resize(n+2);
        Pow[0] = { 1LL, 1LL };
        for (int i = 1; i <= n; ++i) { Pow[i] = Pow[i-1]*p; }
        for (int i = 1; i <= n; ++i) { hs1[i] = hs1[i-1]*p + hs{s[i-1],s[i-1]}; }
    }
    hs get(int l, int r) { return hs1[r]-hs1[l-1]*Pow[r-l+1]; }
};
```


# DP




## 无向图上回路计数

将环视作一条链，起点是环上编号最小的那个点，且链的终点可以指回起点。记 $f(s,i)$ 表示这条链上包含了点集 $s$ 中的所有点，且以 $i$ 结尾的方案数。则
$$
f(s',j)\overset{+}{\leftarrow}f(s,i)\
\begin{cases}
s<s'\\
\mathbin{\rm{lowbit}}(s)<j\\
s[i]=1\\
s[j]=0\\
s'[i]=1\\
s'[j]=1\\
G[i][j]= 1
\end{cases}
$$
当存在一条由终点 $i$ 指向起点 $\mathbin{\rm{lowbit}}(s)$ 的路径时，将 $f(s,i)$ 累加入答案。
```cpp
// CF11D
for (int i = 1; i <= n; ++i) {
	f[1<<(i-1)][i] = 1;
}
for (int s = 0; s < (1<<n); ++s) {
	for (int i = 1; i <= n; ++i) {
		if (!f[s][i]) { continue; }
		for (int j = 1; j <= n; ++j) {
			if (!G[i][j]) { continue; }
			if ((s&-s) > 1<<(j-1)) { continue; }  // 起点不能改
			if(s>>(j-1)&1) {
				if ((s&-s) == 1<<(j-1)) {  // 走回起点
					ans += f[s][i];
				}
			} else {
				f[s|(1<<(j-1))][j] += f[s][i];
			}
		}
	}
}
cout << (ans-m)/2 << "\n";  // 去除无向边，环正走反走算一个
```



## 斜率优化



## 四边形不等式



## wqs二分



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
	- 全局变量记得要清空（测 `assert` 时注意全局！！）
- `(int)a.size()`，否则在和负数比较的时候会出错
- `cin` 读入大数据导致 `TLE`，总输入量达到 `4e6` 就要快读或者优化 `cin`
- 结构体数组赋初值用花括号而不是圆括号
- 答案要取模
- 写拍子
- 考虑初态，什么操作都不做
- 有两维，可以尝试交换枚举顺序
- 【与】和【或】不要搞反
- 看看位运算的优先级，打括号
- 调用系统函数时注意参数类型，如 `__builtin_popcount()` 不能传 `ll`
