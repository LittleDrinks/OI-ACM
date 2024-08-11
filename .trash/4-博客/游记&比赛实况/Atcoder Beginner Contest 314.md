---
tags:
  - 博客
---
# 比赛情况
A 题直接按照题意用字符串输出就行了，很快切掉。

B 题按照题意模拟，但是各种 WA，吃了三发没过先去看 C。

C 题依然是模拟，这道题比较好写一次就过了，回去调 B。

B 题再吃了 3 发罚时终于过了。
先是没有特判输出 0 导致 `vector` 为空 WA/RE。
然后没有注意要从小到大输出，`cmp` 函数漏了一半对下标的判断。
最后是在找数字的时候，按照我的写法，如果全部符合条件，下标不会更新，此时答案为 0，寄。
总之，吃了 6 发罚时，总算是过了。

再开 D 题，一个离线忽略掉对整段序列的大小写修改，只保留最后一次即可。没想到也这么水，40 分钟切完 ABCD。

然后看 EF，一道期望 dp，一道分数取模，直接崩溃，痛骂出题人，摆了一会儿开 G。

G 题一开始想二分，然后想枚举，最后感觉枚举可行，只不过需要一个能在 $O(\log n)$ 以内维护前 $K$ 大所有数之和的东西。
不会，寄。

![image](https://img2023.cnblogs.com/blog/3216291/202308/3216291-20230812221821048-1089816789.png)
rating：571+33=604


# 赛时代码
## A
```cpp
#include <bits/stdc++.h>

using namespace std;

const int MAXN=1e5+5;
string s="1415926535897932384626433832795028841971693993751058209749445923078164062862089986280348253421170679";
int n;

/*
*/

int main()
{
    cin.tie(nullptr) -> sync_with_stdio(false);

    // I.N.
    cin >> n;
    cout << "3.";
    for (int i = 0; i < n; ++i) { cout << s[i]; }

    // E.D.
    return 0;
}
```
## B
```cpp
#include <bits/stdc++.h>

using namespace std;

const int MAXN=105, MAXC=37;
int n, c[MAXN], x;
vector <int> num_to_p[MAXC];

/*
*/

bool cmp(int a, int b)
{
    return (
        (c[a] < c[b])
        || (c[a]==c[b] && a<b)
    );
}

int main()
{
    cin.tie(nullptr) -> sync_with_stdio(false);

    // I.N.
    cin >> n;
    for (int i = 1; i <= n; ++i) {
        cin >> c[i];
        for (int j = 1; j <= c[i]; ++j) {
            int a; cin >> a;
            num_to_p[a].push_back(i);
        }
    }

    // 特判
    cin >> x;
    if (num_to_p[x].empty()) { cout << 0 << endl; return 0; }

    // 对中奖位置排序
    sort(num_to_p[x].begin(), num_to_p[x].end(), cmp);

    // 找到符合条件的人
    int sz = -1;
    for (int i = 1; i < num_to_p[x].size(); ++i) {
        if (c[num_to_p[x][i-1]] < c[num_to_p[x][i]]) {
            sz=i-1; break;
        }
    }
    sz = (sz==-1? num_to_p[x].size()-1: sz);

    // 输出
    cout << sz+1 << endl;
    for (int i = 0; i <= sz; ++i) {
        cout << num_to_p[x][i] << " ";
    }

    // E.D.
    return 0;
}
```
## C
```cpp
#include <bits/stdc++.h>

using namespace std;

const int MAXN=2e5+5;
int n, m;
char s[MAXN];
vector <int> color_to_idx[MAXN];

/*
*/

void operate(int c)
{
    int sz = color_to_idx[c].size();
    char temp = s[color_to_idx[c][sz-1]];
    for (int i = sz-1; i; --i) {
        s[color_to_idx[c][i]] = s[color_to_idx[c][i-1]];
    }
    s[color_to_idx[c][0]] = temp;
}

int main()
{
    cin.tie(nullptr) -> sync_with_stdio(false);

    // I.N.
    cin >> n >> m;
    for (int i = 1; i <= n; ++i) {
        cin >> s[i];
    }
    for (int i = 1; i <= n; ++i) {
        int c; cin >> c;
        color_to_idx[c].push_back(i);
    }

    // 模拟位置变化
    for (int i = 1; i <= m; ++i) { operate(i); }

    // 输出
    for (int i = 1; i <= n; ++i) {
        cout << s[i];
    }

    // E.D.
    return 0;
}
```
## D
```cpp
#include <bits/stdc++.h>

using namespace std;

const int MAXN=5e5+5;
int n, q, status;
char s[MAXN];

/*
*/

char upper_to_lower(char x)
{
    if ( ('a' <= x) && (x <= 'z') ) { return x; }
    return char(x+'a'-'A');
}

char lower_to_upper(char x)
{
    if ( ('A' <= x) && (x <= 'Z') ) { return x; }
    return char(x-'a'+'A');
}

struct OP {
    int t, x;
    char c;
} p[MAXN];

void modify(int t)
{
    for (int i = 1; i <= n; ++i) {
        s[i] = (t==2? upper_to_lower(s[i]): lower_to_upper(s[i]));
    }
}

int main()
{
    cin.tie(nullptr) -> sync_with_stdio(false);

    // I.N.
    cin >> n;
    for (int i = 1; i <= n; ++i) { cin >> s[i]; }

    // 离线，找到最后一次修改大小写，前面全都不用改
    cin >> q;
    int last_mdf_all = q+1;
    for (int i = 1; i <= q; ++i) {
        cin >> p[i].t >> p[i].x >> p[i].c;
        if (p[i].t != 1) { last_mdf_all = i; }
    }

    // 做修改
    for (int i = 1; i <= q; ++i) {
        if (p[i].t == 1) {
            s[ p[i].x ] = p[i].c;
        } else if (i == last_mdf_all) {
            modify(p[i].t);
        }
    }

    // 输出
    for (int i = 1; i <= n; ++i) {
        cout << s[i];
    }

    // E.D.
    return 0;
}
```

# 补题
## G
update：2023-08-14
一个能在 $O(\log n)$ 以内维护前 $K$ 大所有数之和，并且支持单点修改的数据结构，那当然是我氰哎的平衡树啦！
为了补这道题连夜学了[平衡树](https://www.cnblogs.com/LittleDrinks/articles/17629948.html)，结果发现好像有更简单的方法（？）
不管了，就用平衡树补。

首先从平衡树板子那里把平衡树一套全部搬过来。（参数和返回值没改 `long long` 调半天）
设现在遇到了 `tpcnt` 种怪物，用了 `tot` 种护符，每次掐死，选伤害前 `tpcnt-tot` 小的怪物承受伤害。
每当进行单点修改，或者遇到了全新种类的怪物，进行一波增量更新。
下面给出【枚举当前洞穴，做增量更新】的那部分代码：
```cpp
// 枚举当前走到的位置
int tpcnt=0, tot=0;  // 已经遇到的怪物种类
for (int i=1; i <= n; ++i) {  // tot表示选择的护符数量
	// 记录原来的大小排名
	int bf_rk = get_rank(harm[b[i]]);
	
	// 删除第b[i]种怪物的伤害
	del(harm[b[i]]);
	if (bf_rk <= tpcnt-tot) { sum_harm -= harm[b[i]]; }
	
	// 更新伤害
	harm[b[i]] += a[i];
	bool f = false;
	if (!vis[b[i]]) { vis[b[i]]=true; ++tpcnt; f=true; }
	
	// 加入新的伤害
	ins(harm[b[i]]);
	
	// 记录修改之后的大小排名
	int af_rk = get_rank(harm[b[i]]);
	
	// 选择承受[1,tpcnt-tot]的伤害
	// 之前删掉了排名为bf_rank的伤害，现在要加上第tot位的伤害
	if (bf_rk <= tpcnt-tot) {  // 之前减掉了，不管怎么样都得加上来
		sum_harm += get_val(min(af_rk, tpcnt-tot));
	} else if (f) {
		sum_harm += get_val(min(af_rk, tpcnt-tot));
	}
	
	while ( (tot <= m) && (sum_harm >= h) ) {
		ans[++tot] = i-1;
		sum_harm -= get_val(tpcnt-tot+1);
	}
	ans[tot] = i;
}
```
中间的很多东西都是用平衡树维护的，具体代码见[提交记录](https://atcoder.jp/contests/abc314/submissions/44596508)。
