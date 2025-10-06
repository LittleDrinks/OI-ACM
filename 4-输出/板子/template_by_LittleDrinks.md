[TOC]

# 赛前准备

- 打开 vscode 的自动保存功能
- （如需）配置 python 和 g++ 环境变量
- （如需）`ctrl+shift+p` 选择 `C/C++：Edit Configurations（UI）`，配置编译器路径
- 抄一份土制 cph
- 下载样例，右键一键解压缩到 `samples-X` 文件夹



## 土制cph(仅linux)

第一版，使用 `timeout`，仅适用 linux
```python
from os import system as e, listdir as l
from sys import argv
_, q, f = argv  # q: 题号, f: 文件名

# r: 运行命令
# 跑 py 需要 `python3 cph.py A A.py`
if "py" in f:
	r = f"python3 {f}"
else:
	e(f"g++ -std=c++23 -o2 -Wall {f}.cpp -o {f}")
	r = "./" + f

#
d = "samples-" + q.capitalize()
for i in l(d):
	if not "in" in i: continue
	print(i)
	p = d + "/" + i[:-2]
	if e(f"timeout 2 {r}<{p}in>{p}out"): print("TLE or RE")
	else:
		with open(f"{p}out") as o, open(f"{p}ans") as a:
			print("AC" if o.read().split()==a.read().split() else "WA")
```



## 土制cph(全平台)

第二版，使用 `subprocess`，添加彩字，适用 windows 和 linux，支持计算相对误差
```python
from os import listdir as l, system as e
from os.path import join as jp
from sys import argv as a
import subprocess as s
_,q,f = a
if "py" in f:
    cmd = ["python3", f]
else:
    e(f"g++ -std=gnu++20 -O2 -Wall {f}.cpp -o {f}")
    cmd = jp(".", f"{f}.exe")

d='samples-'+q.capitalize()
g,r,p,n='\033[32m','\033[31m','\033[35m','\033[0m'
for i in l(d):
    if not 'in' in i: continue
    t=jp(d,i[:-2])
    print(i,'测评结果 ',end='',flush=1)
    try:
        k = s.run(cmd, timeout=2, stdin=open(f"{t}in"), 
                 stdout=open(f"{t}out", 'w'), stderr=s.PIPE, text=1)
        # c = lambda o,a : all(abs(float(x)-float(y))/max(1,abs(float(y)))<=1e-4 
        #               for x,y in zip(o.read().split(),a.read().split()))
		o = open(f"{t}out").read().split()
		a = open(f"{t}ans").read().split()
		print(f"{g}AC{n}" if o == a else f"{r}WA{n}")
        if k.stderr:print(f'{r}{k.stderr}{n}')
    except s.TimeoutExpired as k:
        print(f'{p}TLE or RE{n}')
        if k.stderr: print(f'{r}{k.stderr}{n}')
```



## 随机数

随机数
```cpp
mt19937_64 rng(chrono::steady_clock::now().time_since_epoch().count());
int Rand(int x, int y) {
	return uniform_int_distribution<int>(x, y)(rng);
}
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
	system("A_gen > _data.in");  // linux 下换成 ./xxx
	system("A_bf < _data.in > A.ans");
	system("A < _data.in > A.ans");
	if (system("fc A.out A.ans")) {  // linux 下换成 diff
		cout << "WA\n"; return 0;
	} else {
		cout << "AC\n";
	}
}
```



# 杂项



## 解除python大数限制
```python
import sys
sys.set_int_max_str_digits(100005)
```



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
ll add(ll x, ll y) { return (x+y)%MOD; }
ll del(ll x, ll y) { return add(x, MOD-y); }
ll mul(ll x, ll y) { return (x*y)%MOD; }
ll qpow(ll a, ll b = MOD - 2) {
	ll res = 1;
	for (; b; b >>= 1) {
		if (b & 1) { res = mul(res, a); }
		a = mul(a, a);
	}
	return res;
}

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
ll sum = accumulate(a.begin(), a.end(), 0LL);

// 填充 1~n，令 a[i]=i
iota(a.begin(), a.end(), 0);
```



## 整数域三分

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



## 运行时间

```cpp
auto start = chrono::high_resolution_clock::now();
auto stop = chrono::high_resolution_clock::now();
auto duration = std::chrono::duration_cast<std::chrono::milliseconds>(stop - start).count();
if (duration > 998) { cout << ans << "\n"; exit(0); }
```



## 火车头
```cpp
#pragma GCC optimize(3)
#pragma GCC optimize("unroll-loops")
```



## 防止umap被卡
```cpp
unordered_map<int,int>mp;
mp.reserve(1024);
mp.max_load_factor(0.25);
```



# 计算几何

https://csacademy.com/app/geometry_widget/
先判断，再计算，精度会好很多
```cpp
const int inf = INT_MAX;
typedef long long ll;
typedef double db;  // 视情况改为 long double
const db eps=1e-8;
const db PI=acos(-1.0);
const db inf = numeric_limits<db>::max();
int sgn(ll x) { return (x>0)-(x<0); }
int sgn(db x) { return (x>eps)-(x<-eps); }
```



## 向量

```cpp
using T = ll;
struct Point {
	T x, y;
    Point(T x=0, T y=0): x(x), y(y) {}
	friend istream& operator>> (istream& is, Point& p) { return is>>p.x>>p.y; }
	friend ostream& operator<< (ostream& os, Point& p) { return os<<p.x<<" "<<p.y; }
	db ang() { return atan2(y, x); }
	bool operator== (const Point &p) const { return sgn(x-p.x)==0 && sgn(y-p.y)==0; }
	bool operator< (const Point &p) const { return x<p.x || (x==p.x && y<p.y); }
	Point operator+ (const Point &p) const { return Point(x+p.x, y+p.y); }
    Point operator- (const Point &p) const { return Point(x-p.x, y-p.y); }
	Point operator* (const T &k) const { return Point(k*x, k*y); }
	Point operator/ (const T &k) const { return Point(x/k, y/k); }
	T operator* (const Point &p) const { return x*p.x + y*p.y; }
	T operator^ (const Point &p) const { return x*p.y - y*p.x; }  // 叉乘时打括号
	T len2() { return (*this)*(*this); }
	db len() { return sqrtl(len2()); }  // 等价于 hypotl(x, y)
	Point rot(db ang) { return Point(x*cos(ang)-y*sin(ang), x*sin(ang)+y*cos(ang)); }
	Point trunc(db l) { return (*this) * (l/len()); }
};
using Vector = Point;

// 判断 c 是否在 ab 的逆时针方向
int toLeft(Point a, Point b, Point c) { return sgn((b-a)^(c-a)); }

// 二维向量夹角，注意特判垂直的情况
db getAngle(Vector a, Vector b) { return fabs(atan2(fabs(a^b), a*b)); }

// ab 夹角是否大于 uv
bool polarCmp(Point a, Point b, Point u, Point v)
{
    int qa = sgn(a*b);
    int qu = sgn(u*v);
    if (qa != qu) {
        return qa < qu;
    } else {
        return abs(a^b) * (u*v) > abs(u^v) * (a*b);
    }
}
```

极角排序 
```cpp
// 直接计算极角
atan2(y, x);            // atan2(n, m) 表示以 m 为极轴，m 转向 n 为正方向
fmod(2*PI+ang(), 2*PI)  // 极角归一化 

// 所有点在一个半平面内的简化版本
bool argcmp(Point a, Point b) { return (a^b) > eps; }

// 全平面极角排序
// 把全平面划分为：下半平面 < 原点 < x 正半轴 < 上半平面 < x 负半轴
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

// STL 中使用 argcmp()，不要忘记传入 &argcmp 作为初始化变量
// 如果 argcmp() 使用 lambda 实现，类型应该为 decltype(argcmp)
set<Point,decltype(&argcmp)> evt{&argcmp};
map<Point,pair<ll,ll>,decltype(&argcmp)> evt{&argcmp};
priority_queue<Point,vector<Point>,decltype(&argcmp)> q{&argcmp};
```



## 点线操作

点 $P$ 到直线 $(A,\vec{v})$ 距离：$\dfrac{\|\vec{v}\times\overrightarrow{AP}\|}{\|\vec{v}\|}$，投影点 $B$ 满足 $\vec{OB}=\vec{OA}+\dfrac{\vec{v}\cdot\overrightarrow{AB}}{\vec{v}^2}\vec{v}$
两直线 $(P_1,\vec{v_1}),(P_2,\vec{v_2})$ 的交点 $Q$ 满足：$\overrightarrow{OQ}=\overrightarrow{OP_1}+\dfrac{\|\vec{v_2}\times\overrightarrow{P_2P_1}\|}{\|\vec{v_1}\times\vec{v_2}\|}\vec{v_1}$
```cpp
struct Line {
    Point a, b;
    Vector v;
	Line(Point a, Point b): a(a), b(b) { v = b - a; }
	int toLeft(Point p) { return sgn(v^(p-a)); }
	bool onSegment(Point p) { return (toLeft(p)==0) && ((p-a)*(p-b)<=0); }
	db distToLine(Point p) { return fabs((v^(p-a))/v.len()); }
	Point proj(Point p) { return a+v*((v*(p-a))/(v*v)); }
	// 用整数操作判断直线与点 C 的距离是否小于（等于）某个值 r
	// 常见于判断直线与圆的位置关系
	// 注意叉乘再相乘可能会爆 ll，所以这里用 __int128
	bool disLess(Point p, i128 r) {
	    i128 xmult = (v^(p-a));
	    return xmult*xmult <= r*r*v.len2();
	}
};

// 判断两条直线是否平行
bool isParallel(Line l1, Line l2)
{
	return sgn(l1.v^l2.v)==0 && sgn(l1.v^(l1.a-l2.a))!=0;
}

// 判断两条线段是否相交
// 先做两次跨立实验判断是否规范相交，再特判至少三点共线的情况
bool hasIntersection(Line L1, Line L2)
{
	// 特判三点共线的情况
    if (L1.onSegment(L2.a)) { return true; }
    if (L1.onSegment(L2.b)) { return true; }
    if (L2.onSegment(L1.a)) { return true; }
    if (L2.onSegment(L1.b)) { return true; }
    // 跨立实验判断两直线是否规范相交
    // 线段端点分布在直线两侧，“线段与线段/线段与直线/直线与直线”是否相交对应修改即可
	function<bool(Line,Point,Point)> crossStanding = [](Line L, Point a, Point b){
        return L.toLeft(a) * L.toLeft(b) == -1;
    };
    return crossStanding(L1, L2.a, L2.b) && crossStanding(L2, L1.a, L1.b);
}

// 求两直线交点
// 求之前应该先判断有没有交点，避免精度误差，同时还要特判共线的情况
Point getIntersection(Line L1, Line L2)
{
    Point P1=L1.a, P2=L2.a;
    Point v1=L1.v, v2=L2.v;
    return P1 + v1 * ( (v2^(P1-P2)) / (v1^v2) );
}

//点P到线段AB的距离公式
double distToSeg(Point p, Point a, Point b) {
    if(a == b) return (p-a).len();
    Point v1=b-a, v2=p-a, v3=p-b;
    if(sgn(v1*v2) < 0) return v2.len();
    if(sgn(v1*v3) > 0) return v3.len();
    return Line(a,b).distToLine(p);
}
```



## 多边形与凸包

任意多边形。可以计算面积和回转数，根据回转数判断点是否在该多边形内。
```cpp
struct Polygon: vector<Point> {
    using vector<Point>::vector;  // 直接使用 vector 的构造函数
    size_t nxt(size_t i) { return i+1==size()? 0: i+1; }
    size_t pre(size_t i) { return i==0? size()-1: i-1; }
	// 求多边形面积
	// 为避免精度误差，可以返回 2*S，即去掉 "0.5*()"
	db area() {
        db res = 0;
        for (size_t i = 0; i < size(); ++i) {
            res += 0.5 * ((*this)[i] ^ (*this)[nxt(i)]);
        }
        return fabs(res);
    }
    // Sunday's algorithm 光线回转法
    // 在整数范围内判断一个点是否在多边形（不保证凸包）内部
    // 亦可用于求某点相对于该多边形的回转数
    pair<bool,int> winding(Point p) {
        int wn = 0, n = this->size();
        for (int i = 0; i < n; ++i) {
            if (Line((*this)[i], (*this)[(i+1)%n]).onSegment(p)) {
            	return { true, -inf };
            }
            int k = Line((*this)[i], (*this)[(i+1)%n]).toLeft(p);
            int d1 = sgn((*this)[i].y - p.y);
            int d2 = sgn((*this)[(i + 1) % n].y - p.y);
            if (k>0 && d1<=0 && d2>0) { wn++; }
            if (k<0 && d2<=0 && d1>0) { wn--; }
        }
        return { wn!=0, wn };
    }
};
```

凸包板子。可以求出平面点集的凸包，判断点是否在凸包内，旋转卡壳。
```cpp
struct Convex: Polygon {
	using Polygon::Polygon;
	// 求平面点集的凸包
	// Andrew 算法，时间复杂度 O(nlogn)
	Convex(vector<Point> p) {
        if (p.empty()) { return; }
        sort(p.begin(), p.end());
        auto check = [&](Point u){
            Point p1=back(), p2=*prev(end(),2);
            return sgn((p1-p2)^(u-p1))<=0;
        };
        for (const Point &u: p) {
            while (size()>1 && check(u)) { pop_back(); }
            push_back(u);
        }
        size_t k = size();
        p.pop_back(); reverse(p.begin(),p.end());
        for (const Point &u: p) {
            while (size()>k && check(u)) { pop_back(); }
            push_back(u);
        }
        pop_back();
    }
    // 判断点是否在凸包内
	bool inConvex(Point p) {
	    int cnt = 0;
	    for (size_t i = 0; i < size(); ++i) {
	        Point p1 = (*this)[i], p2 = (*this)[nxt(i)];
	        if (Line(p1,p2).onSegment(p)) { return true; }
	        cnt += toLeft(p1, p2, p);
	    }
	    if (abs(cnt) == n) { return true; }
	    else { return false; }
	}
	// 旋转卡壳
	template<typename F> void rotcaliper(const F&& func) {
	    auto &p = *this;
	    auto area = [](Point u, Point v, Point w){ return abs((w-u)^(w-v)); };
	    for (size_t i=0, j=1; i < size(); ++i) {
	        func(p[i], p[nxt(i)], p[j]);
	        while (area(p[nxt(j)],p[i],p[nxt(i)]) >= area(p[j],p[i],p[nxt(i)])) { 
	            j = nxt(j);
	            func(p[i], p[nxt(i)], p[j]);
	        }
	    }
	}
	// 旋转卡壳运用：求凸包直径
	T diameter2() {
	    auto &p = *this;
	    if (size() == 1) { return 0; }
	    if (size() == 2) { return (p[0]-p[1]).len2(); }
	    T ans = 0;
	    rotcaliper([&](Point a, Point b, Point c){
	        ans = max( { ans, (a-c).len2(), (b-c).len2() } );
	    }); 
	    return ans;
	}
};

// 旋转卡壳运用：最小矩形覆盖
pair<db,Polygon> rotcaliper(Convex &&p)
{
    db ans = inf;
    Polygon ansp;
    auto area = [](Point a, Point b, Point c) { return fabs((c-a)^(c-b)); };
    for (int i=0, j=1, l=-1, r=-1; i<(int)p.size(); ++i) {
        while (area(p[p.nxt(j)],p[i],p[p.nxt(i)]) >= area(p[j],p[i],p[p.nxt(i)])) {
            j = p.nxt(j);
        }
        if (l == -1) { l=i; r=j; }
        Point v = p[p.nxt(i)]-p[i];
        v = Point(-v.y, v.x);
        while (sgn(v^(p[p.nxt(l)]-p[l])) <= 0) { l = p.nxt(l); }
        while (sgn(v^(p[p.nxt(r)]-p[r])) >= 0) { r = p.nxt(r); }
        Line li(p[i], p[p.nxt(i)]-p[i]), lj(p[j], p[i]-p[p.nxt(i)]);
        Line ll(p[l], v), lr(p[r], v);
        Polygon now = {
            getIntersection(li, ll),
            getIntersection(ll, lj),
            getIntersection(lj, lr),
            getIntersection(lr, li)
        };
        if (sgn(now.area()-ans) < 0) {
            ans = now.area();
            ansp = now;
        }
    }
    return {ans,ansp};
}
```



## 三维向量



# 数据结构



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



## 带权并查集

```cpp
struct DSU {
    int n;
    vector<int> f, siz, pre;
    DSU(int n): n(n),
    			f(vector<int>(n+1)),
    			siz(vector<int>(n+1,1)),
    			pre(vector<int>(n+1))
    {
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
	int find(int l, int x) {  // 倍增写法
	    assert(l < n);
	    if (st[0][l] < x) { return l; }
	    if (query(l, n-1) >= x) { return n; }
	    int ans = l, g = st[0][l];
	    for (int i = I; i >= 0; --i) {
	        if (ans+(1<<i) < n && gcd(g, st[i][ans]) >= x) {
	            g = max(g, st[i][ans]);
	            ans += 1 << i;
	        }
	    }
	    return ans;
	}
};
```



## 树状数组

```cpp
struct BIT {
	int n;
    vector<int> t;
    BIT(int n): n(n), t(n+1) { }  // 注意值域树状数组中 n=tot
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
	    for (int i = __lg(n); i >= 0; --i) {
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



### 二维树状数组
```cpp
struct BIT {
	int n, m;
	vector<vector<int>> t;
	BIT(int n, int m): n(n), m(m), t(n+1, vector<int>(m+1)) { }
	int lowbit(int x) { return x & -x; }
	void modify(int x, int y, int d) {
		for (int i = x; i <= n; i += lowbit(i)) {
			for (int j = y; j <= m; j += lowbit(j)) {
				t[i][j] += d;
			}
		} 
	}
	int query(int x, int y) {
		int res = 0;
		for (int i = x; i; i -= lowbit(i)) {
			for (int j = y; j; j -= lowbit(j)) {
				res += t[i][j];
			}
		}
		return res;
	}
};
```


## 线段树
```cpp
#define lson (p << 1)
#define rson (p << 1 | 1)
#define m ((l+r)>>1)
template<typename Info>
struct segmentTree {
    int n;
    vector<Info> a;
    
    void push_up(int p) {
        a[p] = a[lson] + a[rson];
    }

    template<typename F>
    Info dfs(int p, int l, int r, int x, int y, F&& op) {
        if (y <= l || r <= x) { return Info(); }
        if (x <= l && r <= y) { op(p,l,r); return a[p]; }
        Info res;
        res = res + dfs(lson, l, m, x, y, op);
        res = res + dfs(rson, m, r, x, y, op);
        push_up(p);
        return res;
    }

	template<typename F>
	pair<int,Info> findFirst(int p, int l, int r, int x, int y, F&& pred) {
	    if (y <= l || r <= x) { return {-1,Info()}; }
	    if (x <= l && r <= y && !pred(a[p])) { return {-1,Info()}; }
	    if (l == r-1) { return {l,a[p]}; }
	    pair<int,Info> res = findFirst(lson, l, m, x, y, pred);
	    if (res.first == -1) {
	        res = findFirst(rson, m, r, x, y, pred);
	    }
	    return res;
	}
	
	template<typename F>
	pair<int,Info> findLast(int p, int l, int r, int x, int y, F&& pred) {
	    if (y <= l || r <= x) { return {-1,Info()}; }
	    if (x <= l && r <= y && !pred(a[p])) { return {-1,Info()}; }
	    if (l == r-1) { return {l,a[p]}; }
	    pair<int,Info> res = findLast(lson, m, r, x, y, pred);
	    if (res.first == -1) {
	        res = findLast(rson, l, m, x, y, pred);
	    }
	    return res;
	}

    segmentTree(int n = 0): n(n), a(4 << __lg(n)) { }
    segmentTree(vector<Info>&& b): segmentTree(b.size()) {
        function<void(int,int,int)> build = [&](int p, int l, int r) -> void {
            if (r - l == 1) { a[p] = Info(b[l]); return; }
            build(lson, l, m);
            build(rson, m, r);
            push_up(p);
        };
        build(1, 0, n);
    }

    void modify(int pos, int x) {
        dfs(1, 0, n, pos, pos+1, [&](int p, int l, int r){ a[p].mx = x; });
    }

    int query(int l, int r) {
        return dfs(1, 0, n, l, r+1, [](int,int,int){}).mx;
    }

    void Debug(int p, int l, int r) {
        cerr << format("a[{}] = [ {}, {}, {} ]\n", p, l, r, a[p].mx);
        if (l == r - 1) { return; }
        Debug(lson, l, m);
        Debug(rson, m, r);
    }
    void Debug() { Debug(1, 0, n); }
};
#undef lson
#undef rson
#undef m
struct Info {
    int mx;
    Info(int mx = 0): mx(mx) { }
    friend Info operator+ (Info u, Info v) {
        return Info( max(u.mx, v.mx) );
    }
};
```



### 懒标记线段树


已知一个数列，你需要进行下面三种操作：

- 将某区间每一个数乘上 $x$；
- 将某区间每一个数加上 $x$；
- 求出某区间每一个数的和。

```cpp
#define lson (p << 1)
#define rson (p << 1 | 1)
#define m ((l+r)>>1)
template<typename Info, typename Lazy>
struct segmentTree {
    int n;
    vector<Info> a;
    vector<Lazy> t;

    segmentTree(int n): n(n), a(4 << __lg(n)), t(4 << __lg(n)) { }
    segmentTree(vector<Info>&& vec): segmentTree(vec.size()) {
        function<void(int,int,int)> build = [&](int p, int l, int r) -> void {
            if (r - l == 1) { a[p] = vec[l]; return; }
            build(lson, l, m);
            build(rson, m, r);
            push_up(p); 
        };
        build(1, 0, n);
    }

    void push_up(int p) {
        a[p] = a[lson] + a[rson];
    }

    void apply(int p, Lazy v) {
        a[p] = a[p] + v;
        t[p] = t[p] + v;
    }

    void spread_down(int p) {
        apply(lson, t[p]);
        apply(rson, t[p]);
        t[p] = Lazy();
    }

    template<typename F>
    Info dfs(int p, int l, int r, int x, int y, F&& op) {
        if (y <= l || r <= x) { return Info(); }
        if (x <= l && r <= y) { op(p,l,r); return a[p]; }
        spread_down(p);
        Info res;
        res = res + dfs(lson, l, m, x, y, op);
        res = res + dfs(rson, m, r, x, y, op);
        push_up(p);
        return res;
    }

    template<typename F>
    pair<int,Info> findFirst(int p, int l, int r, int x, int y, F&& pred) {
        if (y <= l || r <= x) { return {-1,Info()}; }
        if (x <= l && r <= y && !pred(a[p])) { return {-1,Info()}; }
        if (l == r-1) { return {l,a[p]}; }
        spread_down(p);
        pair<int,Info> res = findFirst(lson, l, m, x, y, pred);
        if (res.first == -1) {
            res = findFirst(rson, m, r, x, y, pred);
        }
        return res;
    }

    void modify(int x, int y, Lazy v) {
        dfs(1, 0, n, x, y+1, [&](int p, int l, int r){ apply(p, v); });
    }

    Info query(int x, int y) {
        return dfs(1, 0, n, x, y+1, [](int,int,int){});
    }
};
#undef lson
#undef rson
#undef m
struct Info {
    int len;
    ll s;
    Info(ll s=0, int len=1): s(s), len(len) { }
};
struct Lazy {
    ll a, m;
    Lazy(ll a=0, ll m=1): a(a), m(m) { }
};
Info operator+ (Info u, Info v) {
    return Info(add(u.s, v.s), add(u.len,v.len));
}
Info operator+ (Info u, Lazy v) {
    return Info(add(mul(u.s, v.m), mul(v.a, u.len)), u.len);
}
Lazy operator+ (Lazy u, Lazy v) {
    return Lazy(add(mul(u.a, v.m), v.a), mul(u.m, v.m));
}
```



### 吉司机线段树
给出一个长度为 $n$ 的数列 $A$，同时定义一个辅助数组 $B$，$B$ 开始与 $A$ 完全相同。接下来进行了 $m$ 次操作，操作有五种类型，按以下格式给出：

- `1 l r k`：对于所有的 $i\in[l,r]$，将 $A_i$ 加上 $k$（$k$ 可以为负数）。
- `2 l r v`：对于所有的 $i\in[l,r]$，将 $A_i$ 变成 $\min(A_i,v)$。
- `3 l r`：求 $\sum_{i=l}^{r}A_i$。
- `4 l r`：对于所有的 $i\in[l,r]$，求 $A_i$ 的最大值。
- `5 l r`：对于所有的 $i\in[l,r]$，求 $B_i$ 的最大值。

在每一次操作后，我们都进行一次更新，让 $B_i\gets\max(B_i,A_i)$。

```
5 6
1 2 3 4 5
3 2 5
1 1 3 3
4 2 4
2 3 4 1
5 1 5
3 1 4
---
14
6
6
11
```

```cpp
#include <bits/stdc++.h>
using namespace std;
using ll = long long;
using db = long double;
using i128 = __int128;

const int N = 5e5 + 5;
const int inf = 2e9;

struct Info {
    ll s;
    int mx, se, cnt, his;
    Info() : s(-inf), mx(-inf), se(-inf), cnt(0), his(-inf) {
    }
    Info(int val) : s(val), mx(val), se(-inf), cnt(1), his(val) {
    }
    Info(ll s, int mx, int se, int cnt, int his)
        : s(s), mx(mx), se(se), cnt(cnt), his(his) {
    }
};

struct Lazy {
    int a1, a2, a3, a4;
    Lazy() : a1(0), a2(0), a3(0), a4(0) {
    }
    Lazy(int a1, int a2, int a3, int a4) : a1(a1), a2(a2), a3(a3), a4(a4) {
    }
};

#define lson (p << 1)
#define rson (p << 1 | 1)
#define m ((l + r) >> 1)
int vec[N + 5];
Info a[N * 4 + 5];
Lazy t[N * 4 + 5];
void push_up(int p) {
    a[p].s = a[lson].s + a[rson].s;
    a[p].mx = max(a[lson].mx, a[rson].mx);
    a[p].his = max(a[lson].his, a[rson].his);
    if (a[lson].mx == a[rson].mx) {
        a[p].se = max(a[lson].se, a[rson].se);
        a[p].cnt = a[lson].cnt + a[rson].cnt;
    } else if (a[lson].mx > a[rson].mx) {
        a[p].se = max(a[lson].se, a[rson].mx);
        a[p].cnt = a[lson].cnt;
    } else {
        a[p].se = max(a[lson].mx, a[rson].se);
        a[p].cnt = a[rson].cnt;
    }
}
void apply(int p, int l, int r, Lazy v) {
    a[p].s += 1LL * v.a1 * a[p].cnt;
    a[p].s += 1LL * v.a2 * (r - l - a[p].cnt);
    a[p].his = max(a[p].his, a[p].mx + v.a3);
    a[p].mx += v.a1;
    if (a[p].se != -inf) {
        a[p].se += v.a2;
    }

    int a1 = t[p].a1 + v.a1;
    int a2 = t[p].a2 + v.a2;
    int a3 = max(t[p].a3, t[p].a1 + v.a3);
    int a4 = max(t[p].a4, t[p].a2 + v.a4);
    t[p] = Lazy(a1, a2, a3, a4);
}
void spread_down(int p, int l, int r) {
    Lazy v = Lazy(t[p].a2, t[p].a2, t[p].a4, t[p].a4);
    int mx = max(a[lson].mx, a[rson].mx);
    apply(lson, l, m, (a[lson].mx == mx ? t[p] : v));
    apply(rson, m, r, (a[rson].mx == mx ? t[p] : v));
    t[p] = Lazy();
}
void build(int p, int l, int r) {
    if (l == r - 1) {
        a[p] = Info(vec[l]);
        return;
    }
    build(lson, l, m);
    build(rson, m, r);
    push_up(p);
}
void modify_add(int p, int l, int r, int x, int y, int k) {
    if (y <= l || r <= x) {
        return;
    }
    if (x <= l && r <= y) {
        a[p].s += 1LL * k * (r - l);
        a[p].mx += k;
        a[p].his = max(a[p].his, a[p].mx);
        if (a[p].se != -inf) {
            a[p].se += k;
        }
        t[p].a1 += k;
        t[p].a2 += k;
        t[p].a3 = max(t[p].a3, t[p].a1);
        t[p].a4 = max(t[p].a4, t[p].a2);
        return;
    }
    spread_down(p, l, r);
    modify_add(lson, l, m, x, y, k);
    modify_add(rson, m, r, x, y, k);
    push_up(p);
}
void modify_min(int p, int l, int r, int x, int y, int v) {
    if (y <= l || r <= x || v >= a[p].mx) {
        return;
    }
    if (x <= l && r <= y && a[p].se < v) {  // se < v < mx 的条件不分开会 RE
        a[p].s -= 1LL * a[p].cnt * (a[p].mx - v);
        t[p].a1 -= a[p].mx - v;
        a[p].mx = v;
        return;
    }
    spread_down(p, l, r);
    modify_min(lson, l, m, x, y, v);
    modify_min(rson, m, r, x, y, v);
    push_up(p);
}
ll query_sum(int p, int l, int r, int x, int y) {
    if (y <= l || r <= x) {
        return 0;
    }
    if (x <= l && r <= y) {
        return a[p].s;
    }
    ll s = 0;
    spread_down(p, l, r);
    s += query_sum(lson, l, m, x, y);
    s += query_sum(rson, m, r, x, y);
    push_up(p);
    return s;
}
int query_max(int p, int l, int r, int x, int y) {
    if (y <= l || r <= x) {
        return -inf;
    }
    if (x <= l && r <= y) {
        return a[p].mx;
    }
    int mx = -inf;
    spread_down(p, l, r);
    mx = max(mx, query_max(lson, l, m, x, y));
    mx = max(mx, query_max(rson, m, r, x, y));
    push_up(p);
    return mx;
}
int query_hismax(int p, int l, int r, int x, int y) {
    if (y <= l || r <= x) {
        return -inf;
    }
    if (x <= l && r <= y) {
        return a[p].his;
    }
    int mx = -inf;
    spread_down(p, l, r);
    mx = max(mx, query_hismax(lson, l, m, x, y));
    mx = max(mx, query_hismax(rson, m, r, x, y));
    push_up(p);
    return mx;
}
#undef lson
#undef rson
#undef m

int main() {
    ios::sync_with_stdio(0);
    cin.tie(0);
    cout.tie(0);
    int n, q;
    cin >> n >> q;
    for (int i = 0; i < n; ++i) {
        cin >> vec[i];
    }
    build(1, 0, n);
    for (int _ = 1; _ <= q; ++_) {
        int o, l, r;
        cin >> o >> l >> r;
        --l;
        if (o == 1) {
            int k;
            cin >> k;
            modify_add(1, 0, n, l, r, k);
        } else if (o == 2) {
            int v;
            cin >> v;
            modify_min(1, 0, n, l, r, v);
        } else if (o == 3) {
            cout << query_sum(1, 0, n, l, r) << "\n";
        } else if (o == 4) {
            cout << query_max(1, 0, n, l, r) << "\n";
        } else {
            cout << query_hismax(1, 0, n, l, r) << "\n";
        }
    }
}

```


## 笛卡尔树

每个节点的编号满足二叉搜索树的性质。
节点 $i$ 的权值为 $p_i$，每个节点的权值满足小根堆的性质。
```cpp
vector<int> stk;
vector<int> L(n+1), R(n+1);
for (int i = 1; i <= n; ++i) {
	int flag = 0;
	while (!stk.empty()) {
		if (a[stk.back()] > a[i]) {  // 小根堆
			flag = stk.back(); stk.pop_back();
		} else {
			break;
		}
	}
	if (flag) { L[i] = flag; }
	if (!stk.empty()) { R[stk.back()] = i; }
	stk.push_back(i);
}
function<void(int)> dfs = [&](int x){
	cerr << "now node " << x << "\n";
	cerr << ":: L = " << L[x] << "\n";
	cerr << ":: R = " << R[x] << "\n";
	if (L[x]) dfs(L[x]);
	if (R[x]) dfs(R[x]);
};
dfs(stk[0]);
```



## 可持久化



### 可持久化线段树
如题，你需要维护这样的一个长度为 $N$ 的数组，支持如下两种操作：

1. 在某个历史版本上修改某一个位置上的值。输入格式为 `v 1 p c`，即为在版本 $v$ 的基础上，将 $a_{p}$ 修改为 $c$。
2. 访问某个历史版本上的某一位置的值。输入格式为 `v 2 p`，即访问版本 $v$ 中的 $a_{p}$ 的值，注意：**生成一样版本的对象应为 $v$**。

```
5 10
59 46 14 87 41
0 2 1
0 1 1 14
0 1 1 57
0 1 1 88
4 2 4
0 2 5
0 2 4
4 2 1
2 2 2
1 1 5 91
---
59
87
41
87
88
46
```

```cpp
#define m ((l+r)>>1)
const int N=1e6, M=1e6;
int root[M+5];
struct segmentTree {
    int cnt;
    struct node {
        int lson, rson, s;
    } a[N*40+5];
    void build(int &rt, int l, int r, const vector<int>& val) {
        rt = ++cnt;
        if (l == r - 1) { a[rt].s = val[l]; return; }
        build(a[rt].lson, l, m, val);
        build(a[rt].rson, m, r, val);
    }
    void update(int &rt, int l, int r, int p, int x) {
        a[++cnt] = a[rt]; rt = cnt;
        if (l == r - 1) { a[rt].s = x; return; }
        if (p < m) { update(a[rt].lson, l, m, p, x); }
        else       { update(a[rt].rson, m, r, p, x); }
    }
    int query(int rt, int l, int r, int p) {
        if (l == r - 1) { return a[rt].s; }
        if (p < m) { return query(a[rt].lson, l, m, p); }
        else       { return query(a[rt].rson, m, r, p); }
    }
} t;
#undef m

void solve()
{
	int n, m;
	cin >> n >> m;
    vector<int> a(n+1);
    for (int i = 1; i <= n; ++i) { cin >> a[i]; }
    t.build(root[0], 0, n+1, a);
    for (int i = 1; i <= m; ++i) {
        int v, op; cin >> v >> op;
        root[i] = root[v];
        if (op == 1) {
            int p, c;
            cin >> p >> c;
            t.update(root[i], 0, n+1, p, c);
        } else {
            int p; cin >> p;
            cout << t.query(root[i], 0, n+1, p) << "\n";
        }
    }
}

```


### 可持久化Trie
给定一个非负整数序列 $\{a\}$，初始长度为 $N$。  有 $M$ 个操作，有以下两种操作类型：  

1. `A x`：添加操作，表示在序列末尾添加一个数 $x$，序列的长度 $N$ 加 $1$。  
2. `Q l r x`：询问操作，你需要找到一个位置 $p$，满足 $l \le p \le r$，使得：$a[p] \oplus a[p+1] \oplus ... \oplus a[N] \oplus x$ 最大，输出最大值。

```cpp
const int N=2e7+9;
struct Trie {
    int ch[N][2];
    int root[N], cnt[N], top=0, siz=0;
    void insert(int x) {
        root[++top] = ++siz;
        int rt1 = root[top-1], rt2 = root[top];
        for (int i = 24; i >= 0; --i) {
            int b = x >> i & 1;
            ch[rt2][!b] = ch[rt1][!b];
            ch[rt2][b] = ++siz;
            rt1 = ch[rt1][b];
            rt2 = ch[rt2][b];
            cnt[rt2] = cnt[rt1] + 1;
        }
    }
    int query(int l, int r, int x) {
        int ans = 0;
        int rt1 = root[l], rt2 = root[r];
        for (int i = 24; i >= 0; --i) {
            int b = x >> i & 1;
            if (cnt[ch[rt2][!b]] > cnt[ch[rt1][!b]]) {
                ans += (1 << i);
                b = !b;
            }
            rt1 = ch[rt1][b];
            rt2 = ch[rt2][b];
        }
        return ans;
    }
} tr;

void solve()
{
	int n, m;
	cin >> n >> m;
    tr.insert(0);
    int sum = 0; 
    for (int i = 1; i <= n; ++i) {
        int x; cin >> x;
        tr.insert(sum ^= x);
    }
    for (int i = 1; i <= m; ++i) {
        char op; cin >> op;
        if (op == 'A') {
            int x; cin >> x;
            tr.insert(sum ^= x);
        } else {
            int l, r, x;
            cin >> l >> r >> x;
            cout << tr.query(l-1, r, x^sum) << "\n";
        }
    }
}
```



## 分块
```cpp
#include <bits/stdc++.h>
using namespace std;
using ll = long long;
using db = long double;
using i128 = __int128;

ll add(ll x, ll y, ll MOD) { x %= MOD; y %= MOD; return (x + y) % MOD; }
ll mul(ll x, ll y, ll MOD) { x %= MOD; y %= MOD; return (x * y) % MOD; }

const int N = 50000, B = 300;
int n;
int siz, L[B+5], R[B+5], belong[N+5];
ll a[N+5], sum[B+5], tag[B+5];

int main()
{
    ios::sync_with_stdio(0); cin.tie(0); cout.tie(0); 
    cin >> n;
    for (int i = 1; i <= n; ++i) {
        cin >> a[i];
    }

    siz = sqrt(n) + 1;
    for (int i = 1; i <= n; ++i) {
        belong[i] = i / siz + 1;
        sum[belong[i]] += a[i];
        if (belong[i] != belong[i-1]) {
            L[belong[i]] = i;
            R[belong[i-1]] = i - 1;
        }
    }
    L[belong[1]] = 1;
    R[belong[n]] = n;

    for (int i = 1; i <= n; ++i) {
        int o, l, r; ll c;
        cin >> o >> l >> r >> c;
        if (o == 0) {
            if (belong[l] == belong[r]) {
                for (int j = l; j <= r; ++j) {
                    a[j] += c;
                    sum[belong[j]] += c;
                }
            } else {
                for (int j = l; j < L[belong[l]+1]; ++j) {
                    a[j] += c;
                    sum[belong[j]] += c;
                }
                for (int j = L[belong[r]]; j <= r; ++j) {
                    a[j] += c;
                    sum[belong[j]] += c;
                }
                for (int j = belong[l]+1; j < belong[r]; ++j) {
                    sum[j] += c * (R[j] - L[j] + 1);
                    tag[j] += c;
                }
            }
        } else {
            ll ans = 0;
            if (belong[l] == belong[r]) {
                for (int j = l; j <= r; ++j) {
                    ans = add(ans, a[j] + tag[belong[j]], c + 1);
                }
            } else {
                for (int j = l; j < L[belong[l]+1]; ++j) {
                    ans = add(ans, a[j] + tag[belong[j]], c + 1);
                }
                for (int j = L[belong[r]]; j <= r; ++j) {
                    ans = add(ans, a[j] + tag[belong[j]], c + 1);
                }
                for (int j = belong[l]+1; j < belong[r]; ++j) {
                    ans = add(ans, sum[j], c + 1);
                }
            }
            cout << ans << "\n";
        }
    }
}
```



## 莫队
求 $[l,r]$ 中出现了几种数
```cpp
const int N=1e5;
int n, q, a[N+5], x[N+5], y[N+5], ans[N+5], id[N+5];
int cnt[N+5], colcnt;

void move(int p, int d)
{
    cnt[a[p]] += d;
    if (d == 1 && cnt[a[p]] == 1) { ++colcnt; }
    if (d == -1 && cnt[a[p]] == 0) { --colcnt; }
}

int main()
{
    ios::sync_with_stdio(0); cin.tie(0); cout.tie(0); 
    cin >> n >> q;
    iota(id, id+q+1, 0);
    for (int i = 1; i <= n; ++i) { 
        cin >> a[i];
    }
    for (int i = 1; i <= q; ++i) {
        cin >> x[i] >> y[i];
    }
    int siz = sqrt(n) + 1;
    sort(id+1, id+q+1, [&](int a, int b){
        int ra = x[a] / siz;
        int rb = x[b] / siz;
        if (ra == rb) {
            return y[a] < y[b];
        } else {
            return ra < rb;
        }
    });

    int l = 1, r = 0;
    for (int i = 1; i <= q; ++i) {
        int xx = x[id[i]];
        int yy = y[id[i]];
        while (l < xx) { move(l++, -1); }
        while (l > xx) { move(--l, 1); }
        while (r < yy) { move(++r, 1); }
        while (r > yy) { move(r--, -1); }
        ans[id[i]] = colcnt;
    }
}

```


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

邻接矩阵存图，时间复杂度为 $O\left(\dfrac{n^3}{w}\right)$，可以处理 $2000$ 左右的数据。
```cpp
bitset<N> b[N];
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
vector<int> topo;
while (!q.empty()) {
	int u = q.front(); q.pop();
	topo.push_back(u);
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



## 树的直径与中心

树上任意两节点之间最长的简单路径
树形 DP 求法
```cpp
int d[N], mxd;
void dfs(int u, int fa)
{
	for (auto v: G[u]) {
		if (v != fa) {
			dfs(v, u);
			mxd = max(mxd, d[u]+d[v]+1);
			d[u] = max(d[u], d[v]+1);
		}
	}
}
```
两次 DFS 求法，可以把整条直径提取出来，然后在其上找到树的中心
```cpp
void work() {
	int mxd=0, V=0;
	vector<int> f(n+1);
	function<void(int,int,int)> dfs = [&](int u, int fa, int d){
	    f[u] = fa;
	    if (d > mxd) {
	        mxd = d;
	        V = u;
	    }
	    for (auto v: G[u]) {
	        if (v != fa) {
	            dfs(v, u, d+1);
	        }
	    }
	};
	
	// 第一次 dfs，找端点
	mxd = 0;
	dfs(1, 0, 1);
	
	// 第二次 dfs，走直径
	mxd = 0;
	int s = V;
	dfs(V, 0, 1);
	int t = V;
	
	// 把直径存入 vector
	vector<int> R{t};
	while (t != s) {
	    t = f[t];
	    R.push_back(t);
	}
}
```



## 点分治与树的重心

去掉重心所形成的每个连通块的大小都小于等于 $\dfrac{n}{2}$。
```cpp
int n, siz[N], dis[N];
bool vis[N];
vector<pii> G[N];

void calcsiz(int u, int fa, int sum, int &core)
{   // 求树的重心，sum 表述当前树的大小
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


## 基环树

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


## Hierholzer求欧拉路

有向图中欧拉通路存在条件：起点出度比入度大 $1$，终点入度比出度大 $1$，其余点入度等于出度。
有向图中欧拉回路存在条件：所有点入度等于出度。
```cpp
int n, deg[N<<1], cur[N<<1];
bool vis[N];
vector<int> ans;
vector<pair<int,int>> G[N<<1];

void dfs(int u)
{
    for (int &j = cur[u]; j < G[u].size(); ++j) {
        auto [v, i] = G[u][j];
        if (!vis[i]) {
            vis[i] = true;
            dfs(v);
            ans.push_back(i);
        }
    }
}

int main()
{
	int s = 1;
	while (s <= 2*n && deg[s] % 2 == 0) { ++s; }
	if (s > 2 * n) {
	    s = 1;
	    while (!deg[s]) { ++s; }
	}
	
	int cnt = 0;
	for (int i = 1; i <= 2*n; ++i) {
	    cnt += deg[i] % 2;
	}
	if (cnt > 2) {
	    cout << "NO\n"; return;
	}
	
	dfs(s);
	
	if (ans.size() != n) {
	    cout << "NO\n";
	} else {
	    cout << "YES\n";
	    for (auto x: ans) {
	        cout << x << " \n"[x == ans.back()];
	    }
	}
}
```
****


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
        G[u].emplace_back(v, id);
        G[v].emplace_back(u, id);
    }
    void dfs(int u, int uid) {
        stk.push(u);
        dfn[u] = low[u] = ++totdfn;
        for (auto [v, vid]: G[u]) {
            if (uid != vid) {
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
    vector<pii> work() {  // 求桥
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

```cpp
struct PBCC {
    int n, root, totdfn=0, colcnt=0;
    stack<int> stk;
    vector<pii> B;
    vector<int> dfn, low;
    vector<bool> ver;
    vector<vector<int>> pbcc;
    vector<vector<pii>> G;
    PBCC(int n): n(n) {
        G.assign(n+1, {});
        dfn.assign(n+1, 0);
        low.assign(n+1, 0);
        ver.assign(n+1, 0);
    }
    void add_edge(int u, int v, int id) {
        G[u].emplace_back(v, id);
        G[v].emplace_back(u, id);
    }
    void dfs(int u, int uid) {
        stk.push(u);
        int son = 0;
        dfn[u] = low[u] = ++totdfn;
        for (auto [v, vid]: G[u]) {
            if (!dfn[v]) {
                ++son;
                dfs(v, vid);
                low[u] = min(low[u], low[v]);
                if (low[v] >= dfn[u]) {
                    if (u != root) { ver[u] = true; }
                    pbcc.push_back({});
                    while (stk.top() != v) {
                        pbcc.back().push_back(stk.top()); stk.pop();
                    }
                    pbcc.back().push_back(v); stk.pop();
                    pbcc.back().push_back(u); 
                }
            } else if (uid != vid) {
                low[u] = min(low[u], dfn[v]);
            }
        }
        if (u == root) {
            if (son >= 2) { ver[u] = true; }
            if (!son) { pbcc.push_back( { u } ); }
        }
    }
    vector<int> work() {  // 求割点
        vector<int> V;
        for (int i = 1; i <= n; ++i) {
            if (!dfn[i]) { root=i; dfs(i, 0); }
            if (ver[i]) { V.push_back(i); }
        }
        return V;
    }
};
```


## 树哈希
方法一：使用 xor shift 进行哈希混淆，计算有根树的哈希值。
对于无根树，选择以重心为根，计算哈希值最大的那一个。
```cpp
using ull = unsigned long long;
const ull msk = chrono::steady_clock::now().time_since_epoch().count();

// ull h(ull x) { return x * x * x * 1237123 + 19260817; }
// ull f(ull x) { return h(x & ((1 << 31) - 1)) + h(x >> 31); }
ull shift(ull x)
{
    x ^= msk;
    x ^= x << 13;
    x ^= x >> 7;
    x ^= x << 17;
    x ^= msk;
    return x;
}

ull has[N];
void getHash(int u, int fa) 
{
    has[u] = 1;
    for (auto v: G[u]) {
        if (v == fa) { continue; }
        getHash(v, u);
        has[u] += shift(has[v]);
    }
}
```

---

方法二：$f(u)=\sum f(v)\times P(siz_v)$，其中 $P(k)$ 表示第 $k$ 个质数。
记 $g(u)$ 表示以 $u$ 为根时整棵树的哈希值，有换根转移式
$$
g(u) = (g(fa)-f(u)\times P(siz_u))\times P(n-siz_u) + f(u)
$$
```cpp
vector<ll> P = sieve(2e6);  // 0-idx，但是也能用，2e6 以内有 1e5 个质数
const int N=1e5+5;
int n, siz[N];
vector<int> G[N];
ll f[N], g[N];

void dfs(int u, int fa)
{
    siz[u] = 1;
    f[u] = 1;
    for (auto v: G[u]) {
        if (v == fa) {
            continue; 
        }
        dfs(v, u);
        siz[u] += siz[v];
        f[u] = add(f[u], mul(f[v], P[siz[v]]));
    }
}

void change(int u, int fa)
{
    if (fa) {
        ll ffa = del(g[fa], mul<ll>(f[u], P[siz[u]]));
        g[u] = add(f[u], mul<ll>(ffa, P[n-siz[u]]));
    }
    for (auto v: G[u]) {
        if (v == fa) {
            continue;
        }
        change(v, u);
    }
}

void solve()
{
	cin >> n;
    for (int i = 1; i < n; ++i) {
        int u, v;
        cin >> u >> v;
        G[u].push_back(v);
        G[v].push_back(u);
    }
    dfs(1, 0);
    g[1] = f[1];
    change(1, 0);
}
```


## 最大流

dicnic 在普通图上的时间复杂度是 $O (EV^2)$，在单位容量图上的时间复杂度是 $O (E\min(E^{\frac{1}{2}},V^{\frac{2}{3}}))$
```cpp
template<typename T>
struct Flow {
    struct _edge {
        int to;
        T cap;
        _edge(int to, T cap): to(to), cap(cap) { }
    };

    int n;
    vector<_edge> e;
    vector<vector<int>> g;
    vector<int> cur, h;

    Flow() { }
    Flow(int n): n(n), e(), g(n+1), cur(n+1), h(n+1) { }

    bool bfs(int s, int t) {
        h.assign(n+1, -1);
        queue<int> que;
        h[s] = 0;
        que.push(s);
        while (!que.empty()) {
            const int u = que.front(); que.pop();
            for (auto i: g[u]) {
                auto [v, c] = e[i];
                if (c > 0 && h[v] == -1) {
                    h[v] = h[u] + 1;
                    if (v == t) {
                        return true;
                    }
                    que.push(v);
                }
            }
        }
        return false;
    }

    T dfs(int u, int t, T f) {
        if (u == t) { return f; }
        auto r = f;
        for (int &i = cur[u]; i < int(g[u].size()); ++i) {
            const int j = g[u][i];
            auto [v, c] = e[j];
            if (c > 0 && h[v] == h[u] + 1) {
                auto a = dfs(v, t, min(r, c));
                e[j].cap -= a;
                e[j ^ 1].cap += a;
                r -= a;
                if (r == 0) {
                    return f;
                }
            }
        }
        return f - r;
    }

    void add_edge(int u, int v, T c) {
        g[u].push_back(e.size());
        e.emplace_back(v, c);
        g[v].emplace_back(e.size());
        e.emplace_back(u, 0);
    }

    T maxFlow(int s, int t) {
        T ans = 0;
        while (bfs(s, t)) {
            cur.assign(n+1, 0);
            ans += dfs(s, t, numeric_limits<T>::max());
        }
        return ans;
    }

    vector<bool> minCut() {
        vector<bool> c(n+1);
        for (int i = 0; i <= n; ++i) {
            c[i] = (h[i] != -1);
        }
        return c;
    }

    struct edge {
        int fr;
        int to;
        T cap;
        T flow;
    };
    vector<edge> edges() {
        vector<edge> a;
        for (int i = 0; i < int(e.size()); i += 2) {
            edge x;
            x.fr = e[i + 1].to;
            x.to = e[i].to;
            x.cap = e[i].cap + e[i + 1].cap;
            x.flow = e[i + 1].cap;
            a.push_back(x);
        }
        return a;
    }
};

Flow<ll> G(n);  // n 表示最大下标
```



# 数学



## 一些公柿


| 公式名                                   | 内容                                                                                                                                                                                                                                                                                                                                                                                                       |
| ------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| 连续幂和公式                                | $\displaystyle\sum_{i=1}^n(i^2)=\dfrac{n(n+1)(2n+1)}{6}$<br>$\displaystyle\sum_{i=1}^n(i^3)=(\dfrac{n(n+1)}{2})^2$<br>$\displaystyle\sum_{i=1}^n\bigg(i\times(n-i)\bigg)=\dfrac{n(n-1)(n+1)}{6}={n+1\choose 3}$                                                                                                                                                                                          |
| 组合数                                   | ①定义式：$\displaystyle{n\choose k}=\frac{n!}{k!(n-k)!}$；<br>②递推式： $\displaystyle{n\choose m}={n-1\choose m}+{n-1 \choose m-1}$；<br>③曲棍球棒定理：$\displaystyle\sum_{k=r}^n{k\choose r}={n+1\choose r+1}$；<br>④求和：$\displaystyle\sum_{i=0}^n{n\choose i}=2^n$                                                                                                                                                     |
| 几何级数                                  | ① $\dfrac{1}{1-x}=\displaystyle\sum_{k=0}^\infty x^k$；<br>② $\dfrac{x}{(1-x)^2}=\displaystyle\sum_{k=1}^\infty kx^k$                                                                                                                                                                                                                                                                                     |
| 常见的形式幂逆元                              | $A(x)=\sum_{i\ge0}x^i$ 和 $B(x)=1-x$<br>$A(x)=\sum_{i\ge 0} a^ix^i$ 和 $B(x)=1-ax$<br>$A(x)=\sum_{i\ge 0} {i+k-1\choose i}x^i$ 和 $B(x)=(1-x)^k$                                                                                                                                                                                                                                                            |
| 不定方程 $\displaystyle\sum_{i=1}^mx_i=n$ | 正整数解个数为 ${n-1\choose m-1}$<br>非负整数解的个数为 ${n+m-1\choose m-1}$                                                                                                                                                                                                                                                                                                                                             |
| 前缀异或和                                 | $\oplus_{i=1}^ni=\begin{cases}n& n\equiv0\pmod4\\ 1 & n\equiv 1\pmod4\\ n+1&n\equiv2\pmod4\\0&n\equiv3\pmod4\end{cases}$                                                                                                                                                                                                                                                                                 |
| 多重排列                                  | $\dfrac{n!}{\prod_i(k_i!)}$                                                                                                                                                                                                                                                                                                                                                                              |
| 错位排列问题                                | $D(n)=(n-1)\times(D(n-1)+D(n-2))$                                                                                                                                                                                                                                                                                                                                                                        |
| 从 $(1,1)$ 走到 $(n,m)$ 的方案数             | $\displaystyle {n+m-2\choose n-1}$                                                                                                                                                                                                                                                                                                                                                                       |
| 切比雪夫距离、曼哈顿距离                          | $\max\{a,b \}=\dfrac{a+b}{2}+\left\|\dfrac{a-b}{2} \right\|,\ (a,b\ge0)$<br>$\max\bigg\{\|x-a\|,\|y-b\|\bigg\}=\left\|\dfrac{x+y}{2}-\dfrac{a+b}{2}\right\|+\left\|\dfrac{x-y}{2}-\dfrac{a-b}{2}\right\|$<br>$\|x-a\|+\|y-b\|=\max\bigg(\|(x+y)-(a+b)\|, \|(x-y)-(a-b)\|\bigg)$                                                                                                                          |
| 弧微分公式                                 | 二维一般形式：$\int_Lds=\int_L\sqrt{dx^2+dy^2}$<br>直角坐标：$L:y=y(x)(a\leq x\leq b)$，则 $\displaystyle L=\int_a^b\sqrt{1+[y'(x)]^2}dx$<br>参数方程：$L:\begin{cases}x=x(t)\\y=y(t)\end{cases}(\alpha\leq t\leq \beta)$，则 $L=\displaystyle\int_\alpha^\beta\sqrt{[x'(t)]^2+[y'(t)]^2}dt$<br>极坐标：$L:r=r(\theta)(\alpha\leq\theta\leq\beta)$，则 $L=\displaystyle\int_\alpha^\beta\sqrt{[r(\theta)]^2+[r'(\theta)]^2}d\theta$ |



## 线性筛


```cpp
vector<int> sieve(int n)
{
	vector<bool> vis(n+1);
    vector<int> mP(n+1,1);
	vector<int> prime;
	vis[1] = 1;
	for (int i = 2; i <= n; ++i) {
		if (!vis[i]) {
            prime.push_back(i); 
            mP[i] = i;
        }
		for (auto p: prime) {
			if (i*p > n) { break; }
			vis[i*p] = 1;
            mP[i*p] = p;
			if (i%p == 0) { break; }  // 线性筛关键优化
		}
	}
	return prime;
}

// 分解质因数
map<int,int> cnt;
for (int y = x; y > 1; y /= mP[y]) {
    ++cnt[mP[y]];
}
```



## 因数

可以在 $O(\sqrt{V})$ 的时间内求出一个数的因数。
$[1,n]$ 所有数字的因数个数和是 $O(n\log n)$ 的。
$720720$ 是 $10^6$ 内因数最多的数字，其因数个数为 $240$。
$735134400$ 是 $10^9$ 内因数最多的数字，其因数个数为 $1344$。
$10^{12}$ 以内的因数大约是 $3\times 10^3$ 数量级。
```cpp
const int X=1e5;
vector<int> fac[X+5];
for (int i = 1; i <= X; ++i) {
    for (int j = i; j <= X; j += i) {
        fac[j].push_back(i);
    }
}
```



## O(1)gcd
```cpp
const int V = 5000;
int g[V + 5][V + 5];
int gcd(int x, int y) {
    x = abs(x);
    y = abs(y);
    if (x <= V && y <= V && g[x][y] != 0) {
        return g[x][y];
    }
    ll res = x == 0 ? y : gcd(y % x, x);
    if (x <= V && y <= V) {
        g[x][y] = res;
    }
    return res;
}
int lcm(int x, int y) {
    int d = gcd(x, y);
    return d == 0 ? 0 : x / d * y;
}
```



## 排列组合数

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
预处理 $1$ 到 $n$ 的乘法逆元
```cpp
Inv[1] = 1;
for (int i = 2; i <= n; ++i) {
	Inv[i] = (ll)(MOD - MOD / i) * Inv[MOD % i] % MOD;
}
// 另一种写法
fac[0] = Inv[0] = 1;
for (int i = 1; i <= N; ++i) { fac[i] = mul(fac[i-1], i); }
Inv[N] = qpow(fac[N]);
for (int i = N - 1; i; --i) { Inv[i] = mul(Inv[i + 1], i + 1); }
```
直接计算排列组合数
```cpp
const int N=1e5;
ll fac[N+5], Inv[N+5];
ll C(ll n, ll k) { return n==0? 1: fac[n] * Inv[k] % MOD * Inv[n-k] % MOD; }
ll P(ll n, ll k) { return n==0? 1: fac[n] * Inv[n-k] % MOD; }
```
python 高精度版。
```python
import math
def C(n, k):
    return math.factorial(n)//math.factorial(k)//math.factorial(n-k)
```



## Lucas
$$
{n\choose k}={\lfloor n/p \rfloor\choose\lfloor k/p \rfloor}{n\ \mathrm{mod}\  p\choose k\ \mathrm{mod}\ p}\pmod p
$$
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



## 卡特兰数

$Cat(n)$ 的求法：① $\begin{cases}H_1=1\\H_n=\dfrac{4n-2}{n+1}H_{n-1}\end{cases}$；② $H_n=\dfrac{C_{2n}^n}{n+1}$；③ $H_n=C_{2n}^n-C_{2n}^{n-1}$；④ $H_n=\displaystyle\prod_{i=0}^n H_iH_{n-1-i}$
$Cat(n)$ 的意义：①不跨对角线从 $(1,1)$ 走到 $(n,n)$ 的方案数；② $2n$ 个括号所组成的所有合法括号序列的个数；③合法的出栈方案数；④ $n$ 边形划分为若干个三角形的方案数；⑤ $n+1$ 个节点的二叉树形态。
```cpp
ll Cat(ll n) { return C(2*n, n) * qpow(n+1) % MOD; }
```



## 数论分块
$\left\lfloor\dfrac{n}{i}\right\rfloor$ 所在块的右端点为 $\left\lfloor\dfrac{n}{\lfloor n/l\rfloor}\right\rfloor$
```cpp
ll l=1, r=0;
while (l <= n) {
    r = n / (n/l);
    // 统计 ans
    l = r + 1;
}
```
$\left\lceil \dfrac{n}{i}\right\rceil$ 所在块的右端点为 $\left\lfloor \dfrac{n-1}{\lfloor n-1/l\rfloor}\right\rfloor$，注意特判 $l=n$ 的情况
```cpp
ll l=1, r=0;
while (l <= n) {
    r = (l >= n ? n : (n-1)/((n-1)/l));
    // 统计 ans
    l = r + 1;
}
```
二维数论分块，将 `r = n / (n / i)` 替换为 `r = min(n / (n / i), m / (m / i))`



## 高斯消元
```cpp
int Guess(vector<vector<db>> &a)
{
	int n = a.size();
    int c, r;
    for (c = 0, r = 0; c < n; ++c) {
        // 选主元
        int t = r;
        for (int i = r + 1; i < n; ++i) {
            if (fabs(a[i][c]) > fabs(a[t][c])) { t = i; }
        }
        if (sgn(a[t][c]) == 0) { continue; }

        // 行交换
        for (int i = c; i <= n; ++i) { swap(a[t][i], a[r][i]); }

        // 归一化
        for (int i = n; i >= c; --i) { a[r][i] /= a[r][c]; }

        // 前向消元
        for (int i = r + 1; i < n; ++i) {
            if (sgn(a[i][c]) != 0) {
                for (int j = n; j >= c; --j) {
                    a[i][j] -= a[r][j] * a[i][c];
                }
            }
        }
        ++r;
    }

    if (r < n) {
        for (int i = r; i < n; ++i) {
            if (sgn(a[i][n]) != 0) { return 2; }  // 无解
        }
        return 1;  // 无穷多解
    }

    // 回代
    for (int i = n - 1; i >= 0; --i) {
        for (int j = i + 1; j < n; ++j) {
            a[i][n] -= a[i][j] * a[j][n];
        }
    }
    return 0;
}
```



## 线性基
```cpp
struct LinearBasis {
    static const int maxbase = 35;
    bool flag = false;
    ll a[maxbase + 1];
    int tot;
    LinearBasis() {
        memset(a, 0, sizeof a);
        tot = 0;
    }
    LinearBasis(ll *x, int n) {
        LinearBasis();
        build(x, n);
    }
    void build(ll *x, int n) {
        for (int i = 1; i <= n; ++i) insert(x[i]);
    }
    void clear() { memset(a, 0, sizeof a); }
    bool insert(ll t) {
        // 暴力插入一个数，维护的是一个上三角型的线性基矩阵，时间复杂度低，当待插入元素能插入时，返回true
        for (int i = maxbase; i >= 0; --i) {
            if (t & (1ll << i)) {
                if (!a[i]) {
                    a[i] = t;  // 这里表示插入成功
                    break;
                }
                t ^= a[i];
            }
        }
        if (t == 0) flag = true, tot++;
        return t;
    }
    bool query(ll t) {
        // 询问t是否可以被当前线性基表示，不插入
        if (t > queryMax()) return false;
        if (t == 0) return true;
        for (int i = maxbase; i >= 0; --i) {
            if (t & (1ll << i)) {
                if (!a[i]) {
                    return false;
                }
                t ^= a[i];
            }
        }
        return true;
    }
    void Insert(ll t) {
        // 插入一个线性基，利用高斯消元法维护一个对角矩阵
        for (int i = maxbase; i >= 0; --i) {
            if (t >> i & 1) {
                if (a[i])
                    t ^= a[i];
                else {
                    a[i] = t;
                    for (int j = i - 1; j >= 0; --j)
                        if (a[j] && (a[i] >> j & 1)) a[i] ^= a[j];
                    for (int j = i + 1; j <= maxbase; ++j)
                        if (a[j] >> i & 1) a[j] ^= a[i];
                    break;
                }
            }
        }
    }
    LinearBasis merge(const LinearBasis &l1, const LinearBasis &l2) {
        // 得到两个线性基的并
        LinearBasis ret = l1;
        for (int i = maxbase; i >= 0; --i)
            if (l2.a[i]) ret.insert(l2.a[i]);
        return ret;
    }
    LinearBasis intersection(const LinearBasis &l1, const LinearBasis &l2) {
        // 得到两个线性基的交
        LinearBasis all, ret, full;
        ret.clear();
        for (int i = maxbase; i >= 0; --i) {
            all.a[i] = l1.a[i];
            full.a[i] = 1ll << i;
        }
        for (int i = maxbase; i >= 0; --i) {
            if (l2.a[i]) {
                ll v = l2.a[i], k = 0;
                bool flag = true;
                for (int j = maxbase; j >= 0; --j) {
                    if (v & (1ll << j)) {
                        if (all.a[j]) {
                            v ^= all.a[j];
                            k ^= full.a[j];
                        } else {
                            // l2's basis is not in l1's;
                            flag = false;
                            all.a[j] = v;
                            full.a[j] = k;
                            break;
                        }
                    }
                }
                if (flag) {
                    ll v = 0;  // get intersection by k;
                    for (int j = maxbase; j >= 0; --j) {
                        if (k & (1ll << j)) {
                            v ^= l1.a[j];
                        }
                    }
                    ret.insert(v);
                }
            }
        }
        return ret;
    }
    // 询问最值
    ll queryMax() {
        ll ret = 0;
        for (int i = maxbase; i >= 0; --i)
            if ((ret ^ a[i]) > ret) ret ^= a[i];
        return ret;
    }
    ll queryMin() {
        for (int i = 0; i <= maxbase; ++i)
            if (a[i]) return a[i];
        return 0;
    }
    ll Kth_Max(ll k) {
        ll res = 0;
        for (int i = 62; i >= 0; i--)
            if (k & (1LL << i)) res ^= a[i];
        return res;
    }
};
```



## 矩阵快速幂
```cpp
using matrix = vector<vector<ll>>;
matrix IM(int n) {
    matrix I(n, vector<ll>(n));
    for (int i = 0; i < n; ++i) { I[i][i] = 1; }
    return I;
}
matrix operator* (matrix A, matrix B) {
    assert(A.front().size() == B.size());
    int n = A.size(), m = B.size(), k = B.front().size();
    matrix C(n, vector<ll>(m));
    for (int i = 0; i < n; ++i) {
        for (int j = 0; j < k; ++j) {
            for (int x = 0; x < m; ++x) {
                C[i][j] = add(C[i][j], mul(A[i][x], B[x][j]));
            }
        }
    }
    return C;
}
matrix qpow(matrix a, ll b) {
    matrix res = IM(a.size());
    for (; b; b>>=1, a=a*a) {
        if (b & 1) { res = res * a; }
    }
    return res;
}
```



## 自适应辛普森法
```cpp
double simpson(double l, double r) {
    double mid = (l + r) / 2;
    return (r - l) * (f(l) + 4 * f(mid) + f(r)) / 6;  // 辛普森公式
}

double asr(double l, double r, double eps, double ans,
           int step) {  // step是递归的下限
    double mid = (l + r) / 2;
    double fl = simpson(l, mid), fr = simpson(mid, r);
    if (abs(fl + fr - ans) <= 15 * eps && step < 0)
        return fl + fr + (fl + fr - ans) / 15;  // 足够相似的话就直接返回
    return asr(l, mid, eps / 2, fl, step - 1) +
           asr(mid, r, eps / 2, fr, step - 1);  // 否则分割成两段递归求解
}

double calc(double l, double r, double eps) {
    return asr(l, r, eps, simpson(l, r), 12);
}
```



## 多项式



### 暴力乘除
```cpp
using poly = vector<ll>;
poly operator*(const poly &a, const poly &b) {
    int n = a.size();
    int m = b.size();
    poly c(n + m - 1);
    for (int i = 0; i < n; ++i) {
        for (int j = 0; j < m; ++j) {
            c[i + j] = add(c[i + j], mul(a[i], b[j]));
        }
    }
    return c;
}
poly operator/(poly a, poly b) {
    int n = a.size();
    int m = b.size();
    if (b[1] == 0) {
        return a;
    }

    poly c(n - 1);
    ll base = qpow(b[1]);
    for (int i = n - 1; i; --i) {
        ll d = mul(a[i], base);
        c[i - 1] = d;
        a[i - 1] = del(a[i - 1], mul(d, b[0]));
    }
    return c;
}
```



### FFT
```cpp
struct cp {
    db x, y;
    cp(db real=0, db imag=0): x(real), y(imag) { };
    cp operator+ (cp b) const { return {x+b.x, y+b.y}; }
    cp operator- (cp b) const { return {x-b.x, y-b.y}; }
    cp operator* (cp b) const { return {x*b.x - y*b.y, x*b.y + y*b.x}; }
};
using vcp = vector<cp>;
using Poly = vector<int>;
namespace FFT {
    const db PI = acos(-1.0);
    vcp Omega(int L) {
        vcp w(L);
        w[1] = 1;
        for (int i = 2; i < L; i <<= 1) {
            auto w0 = w.begin() + i / 2;
            auto w1 = w.begin() + i;
            cp wn(cos(PI/i), sin(PI/i));
            for (int j = 0; j < i; j += 2) {
                w1[j] = w0[j>>1];
                w1[j+1] = w1[j] * wn;
            }
        }
        return w;
    }
    auto W = Omega(1 << 21);  // NOLINT
    void DIF(cp *a, int n) {
        cp x, y;
        for (int k = n>>1; k; k >>= 1) {
            for (int i = 0; i < n; i += k<<1) {
                for (int j = 0; j < k; ++j) {
                    x = a[i+j];
                    y = a[i+j+k];
                    a[i+j+k] = (a[i+j]-y) * W[k+j];
                    a[i+j] = x+y;
                }
            }
        }
    }
    void IDIT(cp *a, int n) {
        cp x, y;
        for (int k = 1; k < n; k <<= 1) {
            for (int i = 0; i < n; i += k << 1) {
                for (int j = 0; j < k; ++j) {
                    x = a[i+j];
                    y = a[i+j+k] * W[k+j];
                    a[i+j+k] = x-y;
                    a[i+j] = x+y;
                }
            }
        }
        const db Inv = 1.0 / n;
        for (int i = 0; i < n; ++i) {
            a[i].x *= Inv;
            a[i].y *= Inv;
        }
        reverse(a+1, a+n);
    }
}

namespace Polynomial {
    void DFT(vcp &a) { FFT::DIF(a.data(), a.size()); }
    void IDFT(vcp &a) { FFT::IDIT(a.data(), a.size()); }
    int norm(int n) { return 1 << (__lg(n-1) + 1); }
    vcp &dot(vcp &a, vcp &b) {
        for (int i = 0; i < int(a.size()); ++i) {
            a[i] = a[i] * b[i];
        }
        return a;
    }
    Poly operator+ (Poly a, Poly b) {
        int maxlen = max(a.size(), b.size());
        Poly ans(maxlen + 1);
        a.resize(maxlen + 1);
        b.resize(maxlen + 1);
        for (int i = 0; i < maxlen; ++i) { ans[i] = a[i] + b[i]; }
        return ans;
    }
    Poly operator* (ll k, Poly a) {
        Poly ans;
        for (auto i: a) { ans.push_back(k*i); }
        return ans;
    }
    Poly operator* (Poly a, Poly b) {
        int n = a.size() + b.size() - 1;
        vcp c(norm(n));
        for (int i = 0; i < int(a.size()); ++i) { c[i].x = a[i]; }
        for (int i = 0; i < int(b.size()); ++i) { c[i].y = b[i]; }
        DFT(c);
        dot(c, c);
        IDFT(c);
        a.resize(n);
        for (int i = 0; i < n; ++i) {
            a[i] = int(c[i].y * 0.5 + 0.5);
        }
        return a;
    }
}
using namespace Polynomial;
```



### NTT
```cpp
namespace NTT {
const int g = 3;
vector<int> Omega(int L) {
    int wn = qpow(g, MOD / L);
    vector<int> w(L);
    w[L >> 1] = 1;
    for (int i = L / 2 + 1; i <= L - 1; ++i) {
        w[i] = mul(w[i - 1], wn);
    }
    for (int i = L / 2 - 1; i; --i) {
        w[i] = w[i << 1];
    }
    return w;
}
auto W = Omega(1 << 21);
void DIF(int *a, int n) {
    for (int k = n >> 1; k; k >>= 1) {
        for (int i = 0, y; i < n; i += k << 1) {
            for (int j = 0; j < k; ++j) {
                y = a[i + j + k];
                a[i + j + k] = mul(del(a[i + j], y), W[k + j]);
                a[i + j] = add(a[i + j], y);
            }
        }
    }
}
void IDIT(int *a, int n) {
    for (int k = 1; k < n; k <<= 1) {
        for (int i = 0, x, y; i < n; i += k << 1) {
            for (int j = 0; j < k; ++j) {
                x = a[i + j];
                y = mul(a[i + j + k], W[k + j]);
                a[i + j + k] = del(x, y);
                a[i + j] = add(a[i + j], y);
            }
        }
    }
    int Inv = MOD - (MOD - 1) / n;
    for (int i = 0; i < n; ++i) {
        a[i] = mul(a[i], Inv);
    }
    reverse(a + 1, a + n);
}
} // namespace NTT
namespace Polynomial {
using Poly = std::vector<int>;
Poly &operator*=(Poly &a, int b) {
    for (auto &x : a) {
        x = mul(x, b);
    }
    return a;
}
Poly operator*(Poly a, int b) {
    return a *= b;
}
Poly operator*(int a, Poly b) {
    return b * a;
}
void DFT(Poly &a) {
    NTT::DIF(a.data(), a.size());
}
void IDFT(Poly &a) {
    NTT::IDIT(a.data(), a.size());
}
int norm(int n) {
    return 1 << (32 - __builtin_clz(n - 1));
}
void norm(Poly &a) {
    if (!a.empty()) {
        a.resize(norm(a.size()), 0);
    }
}
Poly &dot(Poly &a, Poly &b) {
    for (int i = 0; i < int(a.size()); ++i) {
        a[i] = mul(a[i], b[i]);
    }
    return a;
}
Poly operator*(Poly a, Poly b) {
    int n = a.size() + b.size() - 1, L = norm(n);
    if (a.size() <= 8 || b.size() <= 8) {
        Poly c(n);
        for (int i = 0; i < int(a.size()); ++i) {
            for (int j = 0; j < int(b.size()); ++j) {
                c[i + j] = add(c[i + j], mul(a[i], b[j]));
            }
        }
        return c;
    }
    a.resize(L);
    b.resize(L);
    DFT(a);
    DFT(b);
    dot(a, b);
    IDFT(a);
    a.resize(n);
    return a;
}
} // namespace Polynomial
using namespace Polynomial;
```



# 字符串




## 前缀函数与KMP

```cpp
vector<int> prefixFunction(string s)
{   // fail : 1-index
    int n = s.length();
    vector<int> fail(n + 1);
    for (int i = 1, j = 0; i < n; ++i) {
        while (j && s[i] != s[j]) { j = fail[j]; }
        j += (s[i] == s[j]);
        fail[i + 1] = j;
    }
    return fail;
}
void kmp()
{
	string s, t;
	cin >> s >> t;
	int n = s.length();
	int m = t.length();
	auto fail = prefixFunction(t + "#" + s);
	for (int i = m + 2; i <= n + m + 1; ++i) {
	    if (fail[i] == m) {
	        cout << i - 2*m << "\n";
	    }
	}
	for (int i = 1; i <= m; ++i) { cout << fail[i] << " \n"[i == m]; }
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
typedef pair<ll,ll> hs;
const ll MOD1=1e9+7, MOD2=1e9+9;
const hs p = { 117, 131 };
hs operator+ (hs a, hs b) {
    return hs{ (a.first+b.first)%MOD1, (a.second+b.second)%MOD2 };
}
hs operator- (hs a, hs b) {
    return hs{ (a.first-b.first+MOD1)%MOD1, (a.second-b.second+MOD2)%MOD2 };
}
hs operator* (hs a, hs b) {
    return hs{ a.first*b.first%MOD1, a.second*b.second%MOD2 };
}
struct Hash {
    int n;
    vector<hs> hs1, hs2, Pow;
    Hash(string s) {  // 0-index
        n = s.length();
        hs1.resize(n + 2);
        hs2.resize(n + 2);
        Pow.resize(n + 2);
        Pow[0] = { 1LL, 1LL };
        for (int i = 1; i <= n; ++i) {
            Pow[i] = Pow[i-1] * p;
        }
        for (int i = 1; i <= n; ++i) {
            hs1[i] = hs1[i-1] * p + hs{s[i-1]-'a'+1, s[i-1]-'a'+1};
        }
        for (int i = n; i; --i) {
            hs2[i] = hs2[i+1] * p + hs{s[i-1]-'a'+1, s[i-1]-'a'+1};
        }
    }
    hs get1(int l, int r) {
        return hs1[r] - hs1[l-1] * Pow[r-l+1];
    }
    hs get2(int l, int r) {
        return hs2[l] - hs2[r+1] * Pow[r-l+1];
    }
};
```


## AC自动机
给你一个文本串 $S$ 和 $n$ 个模式串 $T_{1 \sim n}$，请你分别求出每个模式串 $T_i$ 在 $S$ 中出现的次数。
fail 指针：指向次长真公共后缀的结束位置
```cpp
struct AhoCorasick {
    static constexpr int ALPHABET = 26;
    struct Node {
        int len;
        int link;
        array<int,ALPHABET> next;
        Node(): len{0}, link{0}, next{} { }
    };

    vector<Node> t;

    AhoCorasick() {
        init();
    }

    void init() {
        t.assign(2, Node());
        t[0].next.fill(1);
        t[0].len = -1;
    }

    int newNode() {
        t.emplace_back();
        return t.size() - 1;
    }

    int add(const string &a) {
        int p = 1;
        for (auto c: a) {
            int x = c - 'a';
            if (t[p].next[x] == 0) {
                t[p].next[x] = newNode();
                t[t[p].next[x]].len = t[p].len + 1;
            }
            p = t[p].next[x];
        }
        return p;
    }

    void work() {
        queue<int> q;
        q.push(1);

        while (!q.empty()) {
            int x = q.front();
            q.pop();

            for (int i = 0; i < ALPHABET; ++i) {
                if (t[x].next[i] == 0) {
                    t[x].next[i] = t[t[x].link].next[i];
                } else {
                    t[t[x].next[i]].link = t[t[x].link].next[i];
                    q.push(t[x].next[i]);
                }
            }
        }
    }

    int next(int p, int x) {
        return t[p].next[x];
    }

    int link(int p) {
        return t[p].link;
    }

    int len(int p) {
        return t[p].len;
    }

    int size() {
        return t.size();
    }
};

constexpr int N = 2e6+5;
vector<int> G[N];
int cnt[N];

void dfs(int u) 
{
    for (auto v: G[u]) {
        dfs(v);
        cnt[u] += cnt[v];
    }
}

void solve()
{
	int n;
	cin >> n;

    AhoCorasick AC;
    vector<int> pos(n + 1);
    for (int i = 1; i <= n; ++i) {
        string s;
        cin >> s;
        pos[i] = AC.add(s);
    }
    AC.work();

    string T;
    cin >> T;
    int p = 1;
    for (int i = 0; i < int(T.size()); ++i) {
        int x = T[i] - 'a';
        p = AC.next(p, x);
        ++cnt[p];
    }

    for (int i = 1; i < AC.size(); ++i) {
        G[AC.link(i)].push_back(i);
    }
    dfs(1);

    for (int i = 1; i <= n; ++i) {
        cout << cnt[pos[i]] << "\n";
    }
}
```



## 后缀自动机SAM
给定一个只包含小写字母的字符串 $S$。
请你求出 $S$ 的所有出现次数不为 $1$ 的子串的出现次数乘上该子串长度的最大值。
```cpp
//edited by piaoyun from some other's code 
//必须#define int long long 

struct SAM {
    static const int N=1e6+5, S=28;
    int tot=1, last=1, link[N<<1], ch[N<<1][S], len[N<<1], endpos[N<<1];
	// 总点数 tot，点的 index 属于 [1-tot]，空串/根为 1 
	// last 为上一次插入的点
	// link 为点的 parent 树父节点 / 最长 出现位置与自己不同 的后缀
	// ch[n][s] 指节点 n 末尾加字符 s 所转移到的点
	// len 指该节点的串的 最长长度，注意到 最短长度 等于 len[link[n]] + 1 即父节点最长 + 1
	// endpos[n] 参考 get_endpos() 的注释 
    void clear() {
        for (int i = 0; i <= tot; ++i) {
            link[i] = len[i] = endpos[i] = 0;
            for (int k = 0; k < S; ++k) { ch[i][k] = 0; }
        }
        tot = 1; 
        last = 1;
    }

    // 延长一个字符，通常为 [1-26] 
    void extend(int w) {
        int p = ++tot, x = last, r, q;
        endpos[p] = 1;
        for (len[last=p]=len[x]+1; x&&!ch[x][w]; x=link[x]) { ch[x][w]=p; }
        if (!x) { link[p] = 1; }
        else if (len[x] + 1 == len[q=ch[x][w]]) { link[p]=q; }
        else {
            link[r=++tot] = link[q];
            memcpy(ch[r], ch[q], sizeof(ch[r]));
            len[r] = len[x] + 1;
            link[p] = link[q] = r;
            for (; x && ch[x][w] == q; x = link[x]) { ch[x][w] = r; }
        }
    }

    // 注意 vector 占用的空间 
    vector<int> p[N<<1];  //建立 parent 树，以便从上到下 dfs 
    void dfs(int u) {
        int v;
        for (int i = 0; i < int(p[u].size()); ++i) {
            v = p[u][i];
            dfs(v);
            endpos[u] += endpos[v];
        }
    }

    //注意！在使用该方法前，endpos[] 代表每个点作为“终结点”的次数
	//使用该方法后，endpos[] 指在串中出现总次数，即原数组的子树求和
    void get_endpos() {
        for (int i = 1; i <= tot; ++i) { p[i].clear(); }
        for (int i = 2; i <= tot; ++i) {
            p[link[i]].push_back(i);
        }
        dfs(1);
        for (int i = 1; i <= tot; ++i) { p[i].clear(); }
    }

    // 在您不确定是否有抄写错误时再使用该方法 
    // 必须在输入任何数据前自检，此前的数据会被清空 
    static const int STC = 998244353;
    void self_test() {
        clear();
        for (int i = 1; i <= 1000; ++i) { extend(i * i % 26 + 1); }
        ll tmp = 107 * last + 301 * tot;
        for (int i = 1; i <= tot; ++i) {
            tmp = (tmp * 33 + link[i] * 101 + len[i] * 97) % STC;
            for (int k = 1; k < S; ++k) { tmp = (tmp + k * ch[i][k]) % STC; }
        }
        assert("stage 1" && tmp == 393281314);   // stage1 : 检查建树是否正确
        tmp = 0;
        get_endpos();
        for (int i = 1; i <= tot; ++i) { tmp = (tmp * 33 + endpos[i]) % STC; }
        assert("stage 2" && tmp == 178417668);   // stage2 : 检查endpos计算是否正确，如果您修改了endpos[]的含义则会报错
        cout << "Self Test Passed. Remember to delete this function's use. \n";
        clear(); 
    }

	// 调试时可调用 
    void debug_print() {
        for (int i = 1; i <= tot; ++i) {
            cerr << "node : " << i << " father : " << link[i] << " endpos : " << endpos[i] << " len : " << len[i] << "\n"; 
        }
    }

    ll solve() {
		// 在这里输入你自己的解题逻辑
        ll ans = 0;
        get_endpos();
        for (int i = 1; i <= tot; ++i) {
            if (endpos[i] >= 2) { ans = max(ans, (ll)endpos[i]*len[i]); }
        }
        return ans;
    }
} sam;

void solve()
{
    // sam.self_test();
    string s; cin >> s;
    for (auto c: s) { sam.extend(c-'a'+1); }
    cout << sam.solve() << "\n";
}
```



# DP




## 数位DP

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
ll f[L+5][15]
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


## 单调队列优化（滑动窗口）
```cpp
auto work = [&](auto&& cmp) -> void {
	deque<pair<int,int>> q;

	auto update = [&](int id, int x) -> void {
		while (!q.empty() && cmp(x, q.back().second)) {
			q.pop_back();
		}
		q.emplace_back(id, x);
	};

	for (int i = 1; i <= k; ++i) {
		update(i, a[i]);
	}
	for (int i = 1, j = i+k-1; j <= n; ++i, ++j) {
		while (!q.empty() && q.front().first < i) { q.pop_front(); }
		update(j, a[j]);
		cout << q.front().second << " \n"[j == n];
	}
};
```



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



## SOSDP
```cpp
for(int k=0;k<=19;k++)
        for(int i=0;i<=maxn;i++)
            if((i>>k)&1) g[i] += g[i^(1<<k)];   // i 是超集，有第 k 位
for(int k=0;k<=19;k++)
        for(int i=0;i<=maxn;i++)
            if(!((i>>k)&1)) g[i] += g[i^(1<<k)];   // i 是子集，无第 k 位
```



# 烂掉啦
- 心态
	- 如果第一发 `WA` 了，不要急着交下一发，把 `assert` 写全
	- 吃到三发左右，就不要再想着能静态改对了，直接写拍
	- 把自己的目标从【拿到 xxx 排名】转变为【至少过掉手上的这一道题】【把每一分钟都用于思考】
	- 多造数据。想清楚每一步是要干什么、为什么、对不对，如果理不清楚那就不要指望代码能写对
	- 可以尝试写写注释
	- 如果觉得脑袋不清醒，去洗把脸
- 眼瞎 / 脑残
	- 不验样例就开始写，不造数据就随便乱交
	- 要和队友确认题意
	- 答案要取模
	- 多测时，全局变量记得要清空（测 `assert` 时注意全局！！）
	- 【与】和【或】不要搞反
	- 如果一眼看上去感觉完全不可做，很有可能是缺条件或看（想）错条件，先模样例，防止脑残
	- 如果题面或数据范围看错了，从头开始重想，避免下意识地忽略正解
	- 不要忘记换行
- 开 `long long`
	- 叉乘的结果再相乘，需要检查是否爆 `long long`
	- 位运算的时候考虑下 `long long`，比如 `1LL<<i`
	- 注意参数类型，如 `__builtin_popcount()` 不能传 `ll`，`accumulate` 的第三个参数必须传 `ll`
	- （zxp 于 2024.10.29 没开 `long long` 导致凸包求错 debug 2h，望周知）
- 数据类型
	- 写计算几何的时候，注意返回值和参数的数据类型，是 `int` 还是 `double`
	- `(int)a.size()`，否则在和负数比较的时候会出错
	- 结构体数组赋初值用花括号而不是圆括号
	- 严格按照数据类型输入，把 `int` 当成 `double` 输入可能会 TLE
- 上界开小了
	- 取 `min` 时 `inf` 要够大；取 `max` 注意初值是 `0` 或 `-inf`；而涉及两个 `inf` 相加就不能开太极限
	- 写值域相关的数据结构时，数组按值域开而不是按个数开
	- （网络流！）建图的时候考虑清楚点边的意义和数量（如：有可能需要开 $3N$）
- 被卡常了
	- `cin` 读入大数据导致 `TLE`，总输入量达到 `4e6` 就要快读或者优化 `cin`
	- 卡空间题可以用 `vector` 数组按区间长度存数，但不要全用 `vector`，扩容会使用额外的空间
	- 递归时使用 `vector`，注意验算时空复杂度，避免递归导致复杂度多一个 $n$
	- 从 `vector` 改静态数组后，注意局部变量是否有覆盖全局变量，注意全局变量是否被提前清空
- 做图论题先看看图是否连通
- 注意数据范围中带 $0$ 的部分，比如做除法时能不能作分母
	- 存在 $0$ 逆元，不能先乘后除，要写前缀积和后缀积
- 注意位运算的优先级，打括号，如 `(a^b)==1`
- 换个思路
	- 考虑初态，什么操作都不做
	- 有两维，可以尝试交换枚举顺序
	- 如果感觉复杂度过高，可以考虑贪心
	- 看到题目只要求保留小数，在正解思考无果后可以考虑迭代乱搞
