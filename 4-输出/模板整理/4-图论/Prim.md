```cpp
struct node {
    ll v, w;
};
set<pll> q;
vector<node> edge[N];
bool is[N];
ll dis[N];
ll prim() {
    int tot = 0;
    ll ans = 0;
    memset(is, 0, sizeof(is));
    for (int i = 1; i <= n; i++) {
        dis[i] = 1e18;
    }
    dis[1] = 0;
    q.clear();
    for (int i = 1; i <= n; i++) {
        q.insert({dis[i], i});
    }
    while (!q.empty()) {
        int x = q.begin()->second;
        q.erase(q.begin());
        if (dis[x] == 1e18) {
            break;
        }
        tot++;
        ans += dis[x];
        is[x] = 1;
        for (auto j : edge[x]) {
            if (!is[j.v] && j.w < dis[j.v]) {
                q.erase({dis[j.v], j.v});
                dis[j.v] = j.w;
                q.insert({dis[j.v], j.v});
            }
        }
    }
    if (tot != n) {
        return -1;
    } else {
        return ans;
    }
}
```