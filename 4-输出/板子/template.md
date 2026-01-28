```cpp
#include <bits/stdc++.h>
using namespace std;
using ll = long long;
using db = long double;
using i128 = __int128;

template<typename T1,typename T2> ostream& operator<<(ostream& os, const pair<T1,T2>& p) {
    return os<<"("<<p.first<<","<<p.second<<")";
}
template<typename T> ostream& operator<<(ostream& os, const vector<T>& v) {
    os<<"["; for(int i=0;i<v.size();++i) os<<(i?", ":"")<<v[i]; return os<<"]";
}
template<typename T> void gmax(T&x,T y){ x = max(x, y); }
template<typename T> void gmin(T&x,T y){ y = min(x, y); }

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