# 题解 P6154 【游走】

## 题意

给定一个有向无环图，等概率的选一条路径，求路径长度的期望。

## 题解

由期望的公式$E(x)=\sum_{i=1}^\infty i \times p(x=i)$，由于每一条路径出现的概率是相等的，设所有路径长度为$len$，路径条数为$cnt$，所以我们就可以得到路径长度的期望$E(x)=len/cnt$。那么，对于$len$和$cnt$的求法，我们可以使用拓扑排序。令$f_u$为到$u$的路径长度总和，$g_u$为到$u$的路径条数，对于任意一点$u$，考虑其入边，他能被入边上的另外一点$v$更新，到达$u$的路径条数为到达$v$的路径条数之和再加上1，到达$u$的路径的权值之和为到达$v$的路径权值之和+到达$v$的路径数量，具体地，$g_u=\sum g_v+1$，$f_u=\sum(f_v+g_v)$。这样，我们就可以求出答案了。对于取模运算，乘以其逆元即可。

## 代码

```cpp
#include<cstdio>
#include<queue>
using namespace std;
const int maxn=1e6+10,p=998244353;
int n,m,k;
long long len,sum;
struct node{
	int to,next;
}edge[maxn];
int head[maxn],deg[maxn];
long long f[maxn],g[maxn];
void add(int u,int v)
{
	edge[++k].to=v;
	edge[k].next=head[u];
	head[u]=k;
}
queue<int>q;
long long qpow(long long a,long long b)
{
	if(b==0)return 1;
	long long c=qpow(a,b/2);
	c=c*c%p;
	if(b&1)c=c*a%p;
	return c;
}
int main(){
	scanf("%d%d",&n,&m);
	for(int i=1,u,v;i<=m;i++){
		scanf("%d%d",&u,&v);
		add(u,v);
		deg[v]++;
	}
	for(int i=1;i<=n;i++)
		if(!deg[i])
			q.push(i);
	while(q.size()){
		int u=q.front();
		q.pop();
		f[u]++;
		for(int i=head[u];i;i=edge[i].next){
			int v=edge[i].to;
			f[v]=(f[u]+f[v])%p;
			g[v]=(g[v]+g[u]+f[u])%p;
			deg[v]--;
			if(!deg[v])
			q.push(v);
		}
	}
	for(int i=1;i<=n;i++)
		len=(len+g[i])%p,sum=(sum+f[i])%p;
	printf("%lld",len*qpow(sum,p-2)%p);
	return 0;
}
```

