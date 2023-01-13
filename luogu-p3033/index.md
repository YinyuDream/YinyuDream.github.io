# 题解 P3033 【[USACO11NOV]Cow Steeplechase G】

## 二分图最大独立集

本题是给定$n$条垂直于坐标轴的线段，让我们选尽量多的线段，使得他们的交集为空。观察发现，设线段$l_1$和$l_2$交于点$p$，那么$p$只能与其中的一条线段匹配，于是我们可以建立如下的二分图模型：将横着的线段和竖着的线段分别看做两个点集，若两条线段有交点，则在其中连一条边，那么问题就被转化成了二分图的最大独立集问题。因为如果选取的点集中是不存在边连接其中两点的，那么任意两条线段之间就必然没有交点，而我们又求的是最大的独立集，那么满足线段的个数尽量多的要求，根据最大独立集=点数-最大匹配，那么我们只需求得二分图的最大匹配即可。

```cpp
#include<cstdio>
#include<cstring>
#include<algorithm>
using namespace std;
const int maxn=1e3+10;
int n,cnt1,cnt2,ans;
struct seg{
	int c,l,r;
}a[maxn],b[maxn];
int mapp[maxn][maxn],use[maxn],result[maxn];
bool dfs(int x)
{
	for(int i=1;i<=cnt2;i++){
		if(mapp[x][i]&&!use[i]){
			use[i]=1;
			if(!result[i]||dfs(result[i])){
				result[i]=x;
				return true;
			}
		}
	}
	return false;
}
int main(){
	scanf("%d",&n);
	for(int i=1,_a,_b,_c,_d;i<=n;i++){
		scanf("%d%d%d%d",&_a,&_b,&_c,&_d);
		if(_a==_c)a[++cnt1]={_a,_b,_d};
		if(_b==_d)b[++cnt2]={_b,_a,_c};
	}
	for(int i=1;i<=cnt1;i++)
		if(a[i].l>a[i].r)
			swap(a[i].l,a[i].r);
	for(int i=1;i<=cnt2;i++)
		if(b[i].l>b[i].r)
			swap(b[i].l,b[i].r);
	for(int i=1;i<=cnt1;i++){
		for(int j=1;j<=cnt2;j++){
			if(a[i].l<=b[j].c&&a[i].r>=b[j].c&&b[j].l<=a[i].c&&b[j].r>=a[i].c){
				mapp[i][j]=1;
			}
		}
	}
	for(int i=1;i<=cnt1;i++){
		memset(use,0,sizeof use);
		if(dfs(i))ans++;
	}
	printf("%d",n-ans);
	return 0;
}
```

