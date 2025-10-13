# 赛前准备
- 打开 vscode 的自动保存功能
- （如需）配置 python 和 g++ 环境变量
- （如需）`ctrl+shift+p` 选择 `C/C++：Edit Configurations（UI）`，配置编译器路径
- 抄一份土制 cph
- 下载样例，右键一键解压缩到 `samples-X` 文件夹

```python
from os import listdir as l, system as e
from os.path import join as jp
from sys import argv
import subprocess as s
_,q,f = argv
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


# template
```cpp
#include <bits/stdc++.h>
using namespace std;
using ll = long long;
using db = long double;
using i128 = __int128;

template<typename T1,typename T2>
ostream& operator<<(ostream& os, const pair<T1,T2>& p) {
    return os << "( " << p.first << ", " << p.second << " )";
}
template<typename T>
ostream& operator<<(ostream& os, const vector<T>& v) {
    os << "[ ";
    if (v.size()) os << v[0];
    for (int i = 1; i < int(v.size()); ++i) {
        os << ", " << v[i];
    }
    return os << " ]";
}

void solve()
{
    int n;
    cin >> n;
}

int main()
{
    ios::sync_with_stdio(0); cin.tie(0); cout.tie(0); 
    int t = 1;
    cin >> t;
    while (t--) { solve(); }
}

```


# 对拍

.cpp 脚本
```cpp
while (1) {
	system("./A_gen > _data.in");
	system("./A_bf < _data.in > A.ans");
	system("./A < _data.in > A.ans");
	if (system("diff A.out A.ans")) {  // windows 下换成 fc
		cout << "WA\n"; return 0;
	} else {
		cout << "AC\n";
	}
}
```
.sh 脚本
```cpp
#!/bin/bash
P=$1
g++ -std=gnu++20 -O2 "${P}.cpp" -o "${P}"
g++ -std=gnu++20 -O2 "${P}_bf.cpp" -o "${P}_bf"
g++ -std=gnu++20 -O2 "${P}_gen.cpp" -o "${P}_gen"
while true; do
    ./"${P}_gen" > ".in"
    ./"${P}" < ".in" > ".out"
    ./"${P}_bf" < ".in" > ".ans"
    if diff ".out" ".ans" > /dev/null; then
        echo "AC"
    else
        echo "WA"
        break
    fi
done
```



# 随机数

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
