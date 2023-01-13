# 题解 P2726 【[SHOI2005]树的双中心】

## 题解：

观察表达式，发现要求的是两个点，满足每个点的权值之和乘以该点到这两点的距离的较小值之和最小。我们可以发现，对于树上的两个点，到一个点为最小距离的集合和到另外一个点为最小距离的点的集合之间是没有交集的。故我们可以枚举边，对两颗子树分别求带权值的树的重心，则求得的正好是表达式的值。但是这任然是$O(n^2)$的，观察方程:$f_v=f_u+size_u-size_v$，若$v$比$u$更优，当且仅当$2size_v>size_1$，且满足$2size_v>size_1$的$v$有且仅有一个，故可以处理出最大儿子和次大儿子，当连接最大儿子的边断裂时，使用次大儿子计算，此时时间复杂度即为$O(nh)$的。

## 代码：
```cpp
#include<cstdio>
#include<algorithm>
#define int long long
using namespace std;
const int maxn=1e6+10;
int n,k,cut,ans=0x7f7f7f7f7f7f7f7fll;
struct node{
	int to,next;
}edge[maxn];
int head[maxn],fa[maxn];
int w[maxn],son1[maxn],son2[maxn],size[maxn],a[maxn],deep[maxn];
void add(int u,int v)
{
	edge[++k].to=v;
	edge[k].next=head[u];
	head[u]=k;
}
void dfs(int u,int f)
{
	fa[u]=f;
	size[u]=w[u];
	a[u]=w[u]*deep[u];
	for(int i=head[u];i;i=edge[i].next)
	{
		int v=edge[i].to;
		if(v!=f)
		{
			deep[v]=deep[u]+1;
			dfs(v,u);
			size[u]+=size[v];
			a[u]+=a[v];
			if(size[son1[u]]<=size[v]){
				son2[u]=son1[u];
				son1[u]=v;
			}else if(size[son2[u]]<=size[v]){
				son2[u]=v;
			}
		}
	}
}
void get_ans(int u,int sz,int num,int &cnt)
{
	cnt=min(cnt,num);
	int v=son1[u];
	if(size[son1[u]]<size[son2[u]]||v==cut)v=son2[u];
	if(v&&size[v]*2>size[u])get_ans(v,sz,num+sz-2*size[v],cnt);
}
void solve(int u)
{
	for(int j=head[u];j;j=edge[j].next)
	{
		int v=edge[j].to;
		if(v!=fa[u])
		{
			int _a=0x7f7f7f7f7f7f7f7fll,b=0x7f7f7f7f7f7f7f7fll;
			cut=v;
			for(int i=u;i;i=fa[i])
			size[i]-=size[v];
			get_ans(1,size[1],a[1]-a[v],_a);
			get_ans(v,size[v],a[v]-size[v]*deep[v],b);
			ans=min(ans,_a+b);
			for(int i=u;i;i=fa[i])
			size[i]+=size[v];
			solve(v);
		}
	}
}
main(){
	//freopen("center.in","r",stdin);
	//freopen("center.out","w",stdout);
	scanf("%lld",&n);
	for(int i=1;i<n;i++)
	{
		int u,v;
		scanf("%lld%lld",&u,&v);
		add(u,v);
		add(v,u);
	}
	for(int i=1;i<=n;i++)
		scanf("%lld",&w[i]);
	dfs(1,0);
	solve(1);
	printf("%lld",ans);
	return 0;
}
```

