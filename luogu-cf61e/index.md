# 题解 CF61E 【Enemy is weak】

## 题解：

本题是在数列$a_i$中，求满足$i<j<k$且$a_i>a_j>a_k$的三元组的个数。对于本题，我们可以枚举三元组的中间位置$i$，在其左边求得比他小的数的数的数量$a_i$，在其右边求得比他大的数的数量$b_i$，则最终答案为 $\sum_{i=1}^na_ib_i$。具体地，我们可以用权值树状数组维护出$1-i$的数字出现的数量，我们从左往右遍历，对于数$a_i$，我们首先查询小于$a_i$的数的数量，并在树状数组中更新该数字。按照同样的方法可以求出$c_i$。本题数据较大，需注意离散化。

## 代码：
```cpp
#include<cstdio>
#include<cstring>
#include<algorithm>
using namespace std;
const int maxn=1e6+10;
int n;
long long ans;
int a[maxn],l[maxn],r[maxn],tree[maxn],b[maxn];
void lsh()
{
	for(int i=1;i<=n;i++)
		b[i]=a[i];
	sort(b+1,b+n+1);
	int m=unique(b+1,b+n+1)-b-1;
	for(int i=1;i<=n;i++)
	a[i]=lower_bound(b+1,b+m+1,a[i])-b;
}
inline int lowbit(int x)
{
	return x&-x;
}
void update(int x,int val)
{
	for(int i=x;i<=n;i+=lowbit(i))tree[i]+=val;
}
int query(int x)
{
	int ans=0;
	for(int i=x;i;i-=lowbit(i))ans+=tree[i];
	return ans;
}
int main(){
	scanf("%d",&n);
	for(int i=1;i<=n;i++)
		scanf("%d",&a[i]);
	lsh();
	for(int i=1;i<=n;i++)
	l[i]=(query(n)-query(a[i])),update(a[i],1);
	memset(tree,0,sizeof tree);
	for(int i=n;i>=1;i--)
	r[i]=query(a[i]-1),update(a[i],1);
	for(int i=1;i<=n;i++)
		ans+=1ll*l[i]*r[i];
	printf("%lld",ans);
	return 0;
}
```

