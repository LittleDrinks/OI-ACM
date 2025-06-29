给你一个文本串 $S$ 和 $n$ 个模式串 $T_{1 \sim n}$，请你分别求出每个模式串 $T_i$ 在 $S$ 中出现的次数。
```cpp
#include<bits/stdc++.h>
using namespace std;
const int N=2e6+5;
int n,ch[N][26],cnt=0,fail[N],tag[N],id[N];
char s[N];
vector<int> e[N];
void insert(char *t,int k){
	int p=0;
	for(int i=0;t[i];i++){
		int c=t[i]-'a';
		if(!ch[p][c]) ch[p][c]=++cnt;
		p=ch[p][c];
	}
	id[k]=p;
}
void build(){
	queue<int> q;
	for(int i=0;i<26;i++){
		if(ch[0][i]) q.push(ch[0][i]);
	}
	while(!q.empty()){
		int u=q.front();q.pop();
		for(int i=0;i<26;i++){
			int v=ch[u][i];
			if(v) fail[v]=ch[fail[u]][i],q.push(v);
			else ch[u][i]=ch[fail[u]][i];
		}
	}
}
void query(char *t){
	for(int i=0,k=0;t[i];i++){
		k=ch[k][t[i]-'a'];
		tag[k]++;
	}
}
void dfs(int u){
	for(auto v:e[u]){
		dfs(v);
		tag[u]+=tag[v];
	}
}
int main(){
	cin>>n;
	for(int i=1;i<=n;i++){
		char t[N];
		cin>>t;
		insert(t,i);
	}
	cin>>s;
	build();
	query(s);
	for(int i=1;i<=cnt;i++)
		e[fail[i]].push_back(i);
	dfs(0);
	for(int i=1;i<=n;i++){
		cout<<tag[id[i]]<<endl;
	}
	return 0;
}
```