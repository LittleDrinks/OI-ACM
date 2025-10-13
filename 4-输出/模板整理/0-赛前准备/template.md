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