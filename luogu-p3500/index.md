# 题解 P3500 【[POI2010]TES-Intelligence Test】

## 题解：

本题是给定一个序列$a$，多次询问某数列是否是该序列的子序列。对于每一个询问的序列$b$，对于$b_1$，我们可以在原序列在$[1,n]$中寻找第一个$b_i$出现的位置$p_1$,再在$[p_1+1,n]$中寻找$b_2$出现的位置$p_2$，以此类推，如果对于存在一个位置不能找到则无解，反之有解。那么如何快速求解在原序列中第一次出现的位置呢？我们可以用二分查找，对于每一个数建立一个vector，在其中二分查找出第一个大于$p$的位置。

## 代码：
```cpp
#include<cstdio>
#include<vector>
#include<algorithm>
using namespace std;
const int maxn=1e6+10;
int n,m;
int a[maxn],c[maxn];
vector<int>pos[maxn];
int read(){
	int x=0,sign=1;
	char ch=getchar();
	while(ch>'9'||ch<'0')
	{
		if(ch=='-')sign=-sign;
		ch=getchar();
	}
	while(ch>='0'&&ch<='9')
	x=x*10+ch-48,
	ch=getchar();
	return x*sign;
}
int main(){
	n=read();
	for(int i=1;i<=n;i++)
		a[i]=read();
	for(int i=1;i<=n;i++)
		pos[a[i]].push_back(i);
	m=read();
	for(int i=1;i<=m;i++)
	{
		int l=read(),p=0,book=0;
		for(int j=1;j<=l;j++)
		c[j]=read();
		for(int j=1;j<=l;j++)
		{
			auto t=upper_bound(pos[c[j]].begin(),pos[c[j]].end(),p);
			if(t==pos[c[j]].end()){
				book=1;
				puts("NIE");
				break;
			}
			else p=*t;
		}
		if(!book)
		puts("TAK");
	}
	return 0;
}
```

