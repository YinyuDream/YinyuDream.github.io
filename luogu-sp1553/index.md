# 题解 SP1553 【BACKUP - Backup Files】

## 题解：

本题是有n个城市，用k个电缆使得城市两两配对，且任意一个城市只能链接一个电缆，求最小的距离和。观察发现，我们一定是配对两两相邻的城市，这样才能使得距离最小。所以就可以转化为[P1792](https://www.luogu.com.cn/problem/P1792)的模型，把相邻的城市看做一个点，选点不能相邻，使得和最小。这是反悔贪心的一道题，首先考虑一种错误的贪心：选最大的，然后在其他合法的位置选最大的，但是这样贪心很容易找出反例。但我们发现，如果不选最大的，那么一定会选最大值左右两个位置。所以，我们可以想到如下算法：首先将所有元素入小根堆，每次出堆时，将左右两边的元素标记，并将左边的元素加右边元素减去该元素的新点放入堆，并在链表上更新该点的左右值，重复该过程，直至循环次数达到k。

## 代码：
```cpp
#include<cstdio>
#include<queue>
#include<cstring>
using namespace std;
const int maxn=1e6+10;
int n,k,T;
int num[maxn],net[maxn],pre[maxn],book[maxn];
long long a[maxn],ans;
struct node{
	int id;
	long long w;
	node(int _id,long long _w){
		id=_id,w=_w;
	}
	friend bool operator<(node a,node b){
		return a.w>b.w;
	}
};
priority_queue<node>q;
int main(){
	scanf("%d",&T);
	while(T--)
	{
		ans=0;
		memset(book,0,sizeof book);
		while(q.size())q.pop();
		scanf("%d%d",&n,&k);
		for(int i=1;i<=n;i++)
			scanf("%d",&num[i]);
		for(int i=1;i<n;i++)
			a[i]=num[i+1]-num[i];
		for(int i=1;i<=n-1;i++)
			net[i]=i+1;
		for(int i=1;i<=n-1;i++)
			pre[i]=i-1;
		a[0]=a[n]=0x7f7f7f7f7fll;
		for(int i=1;i<n;i++)
			q.push(node(i,a[i]));
		for(int i=1;i<=k;i++)
		{
			while(book[q.top().id])q.pop();
			node t=q.top();
			q.pop();
			ans+=t.w;
			book[pre[t.id]]=book[net[t.id]]=1;
			a[t.id]=a[pre[t.id]]+a[net[t.id]]-a[t.id];
			q.push(node(t.id,a[t.id]));
			net[t.id]=net[net[t.id]];
			pre[t.id]=pre[pre[t.id]];
			pre[net[t.id]]=t.id;
			net[pre[t.id]]=t.id;
		}
		printf("%lld\n",ans);
	}
	return 0;
}
```


