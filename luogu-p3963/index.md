# 题解 P3963 【[TJOI2013] 奖学金】

## 题解：

本道题可以先按照成绩排序，再枚举成绩的中位数求解。对于每一个成绩作为中位数的学生，可以求出成绩小于中位数的学生的最小的$n/2$的奖学金之和$a$以及成绩大于中位数的学生的最小的$n/2$的奖学金之和$b$，设作为中位数的学生的奖学金为$c$，若$a+b+c<=f$则该成绩作为中位数是合法的。这样，我们就可以求得答案了。对于$a$和$b$的求解，我们可以使用堆。我们从左往右遍历，若当前位置$i$的值$t_i$大于堆顶元素$m$，则选择当前元素，将堆顶元素出堆，将该元素入堆，$a_i=a_{i-1}-m+t_i$，否则，$a_i=a_{i-1}$。

## 代码：
```cpp
#include<cstdio>
#include<queue>
#include<algorithm>
using namespace std;
const int maxn=1e6+10;
int n,c,f,ans;
int suma[maxn],sumb[maxn];
struct node{
	int a,b;
}z[maxn];
bool cmp(node a,node b)
{
	return a.a<b.a;
}
priority_queue<int>q;
int main(){
	scanf("%d%d%d",&n,&c,&f);
	for(int i=1;i<=c;i++)
		scanf("%d%d",&z[i].a,&z[i].b);
	sort(z+1,z+c+1,cmp);
	for(int i=1;i<=n/2;i++)
		q.push(z[i].b),suma[i]=suma[i-1]+z[i].b;
	for(int i=n/2+1;i<=c;i++)
	{
		if(z[i].b<q.top())suma[i]=suma[i-1]-q.top()+z[i].b,q.pop(),q.push(z[i].b);
		else suma[i]=suma[i-1];
	}
	while(q.size())q.pop();
	for(int i=c;i>=c-n/2+1;i--)
		q.push(z[i].b),sumb[i]=sumb[i+1]+z[i].b;
	for(int i=c-n/2;i>=1;i--)
	{
		if(z[i].b<q.top())sumb[i]=sumb[i+1]-q.top()+z[i].b,q.pop(),q.push(z[i].b);
		else sumb[i]=sumb[i+1];
	}
	for(int i=n/2+1;i<=c-n/2;i++)
	{
		if(suma[i-1]+sumb[i+1]+z[i].b<=f)
		ans=z[i].a;
	}
	printf("%d",ans!=0?ans:-1);
	return 0;
}
```

