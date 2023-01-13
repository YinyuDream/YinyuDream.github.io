# 题解 P4735 【最大异或和】

## 可持久化01trie

设$s_n=a_1\oplus a_2\oplus a_3\oplus ...\oplus a_n$,对于题中所给的对一段区间的询问，我们就可以转化为前缀异或，即$a_p\oplus a_{p+1}\oplus a_{p+2}\oplus ...\oplus a_n\oplus x=s_{p-1}\oplus s_n\oplus x$。这样，问题就转化为了在给定的区间$[L,R]$中，求出与$s_n\oplus x$异或的答案最大的数。于是我们可以建$n$棵01trie,第$i$棵代表区间$[1,i]$的值,并在每个节点存储数字个数,对于每个询问，我们只需将两颗树相减就得到了当前区间的信息。在01trie上，我们从高位到低位贪心，假设$x$最高位为1，如果当前区间由最高为0的数，则一定要选，否则无论如何都不可能比他更优，以此类推，由高到低依次确定每一位的值。

```cpp
#include<cstdio>
#include<iostream>
using namespace std;
const int maxn=3e7+10;
int n,m,k;
int a[maxn],sum[maxn];
struct tire{
	int size,son[2];
}tree[maxn];
int root[maxn];
int build(int x)
{
	int now=++k;
	tree[now].size=1;
	if(x==0)return now;
	tree[now].son[0]=build(x-1);
	return now;	
}
int update(int pre,int x,int pos)
{
	int now=++k;
	if(pos==0){
		tree[now].size=tree[pre].size+1;
		return now;
	}
	tree[now]=tree[pre];
	tree[now].son[1&(x>>(pos-1))]=update(tree[pre].son[1&(x>>(pos-1))],x,pos-1);
	tree[now].size=tree[tree[now].son[0]].size+tree[tree[now].son[1]].size;
	return now;
}
int query(int pre,int now,int x,int pos)
{
	if(!pos)return 0;
	return (tree[tree[now].son[1^(1&(x>>(pos-1)))]].size-tree[tree[pre].son[1^(1&(x>>(pos-1)))]].size)?
			query(tree[pre].son[1^(1&(x>>(pos-1)))],tree[now].son[1^(1&(x>>(pos-1)))],x,pos-1)+(1<<(pos-1)):
			query(tree[pre].son[1&(x>>(pos-1))],tree[now].son[1&(x>>(pos-1))],x,pos-1);
}
int main(){
	scanf("%d%d",&n,&m);
	for(int i=1;i<=n;i++)
		scanf("%d",&a[i]),
		sum[i]=sum[i-1]^a[i];
	root[0]=++k;
	for(int i=0;i<=n;i++)
		root[i+1]=update(root[i],sum[i],25);
	for(int i=1,l,r,x;i<=m;i++){
		char opt;
		scanf("\n%c",&opt);
		if(opt=='A')scanf("%d",&x),sum[n+1]=sum[n]^x,root[n+2]=update(root[n+1],sum[n+1],25),n++;
		else{
			scanf("%d%d%d",&l,&r,&x);
			printf("%d\n",query(root[l-1],root[r],x^sum[n],25));
		}
	}
	return 0;
}
```

