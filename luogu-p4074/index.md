# 题解 P4074 【[WC2013]糖果公园】

## 题意
给定一棵树，树上有$m$种糖果,美味指数为$V_i$，共有$q$次操作，每次询问树上的一条路径$u->v$，第$i$次品尝$j$种糖果的新奇指数为$W_i$，并且愉悦指数$H$增加$V_j \times W_i$，求最终的愉悦指数或把点$x$的糖果类型改为$y$。
## 题解
树上带修改莫队。
- 考虑把树用欧拉序变成序列,对于每一条询问的路径，可以转化为在序列上的询问。如果两个点的LCA不等于其中之一，序列上的区间应该为两个点在欧拉序上第一次的位置之间的区间∪LCA，否则，序列上的区间应为第一个点后出现的位置到第二个点第一次出现的位置。在区间内，只有出现一次的点才会被记入答案。

- 维护当前区间内各种颜色出现的次数。当区间内的某个点的个数变为1时，愉悦指数增加(该点的颜色出现次数+1)$\times$该点颜色的美味指数，同时，该颜色出现次数+1.

- 对于修改操作，可以仿照普通带修改莫队，增加一维时间戳，记录每次查询操作在第几次修改操作之后以及当前序列是第几次修改操作之后的。当序列的时间戳i小于询问的时间戳j时，执行j到i的修改操作，反之，还原i到j的修改操作。如果修改的节点在询问的节点之中，维护相应的信息。

## 代码
```cpp
#include<cmath>
#include<cstdio>
#include<algorithm>
using namespace std;
const int maxn=1e6+10;
int n,m,q,k,cnt,t1,t2,l=1,r,now,block;
int v[maxn],w[maxn],a[maxn];
long long num,ans[maxn];
struct node{
    int to,next;
}edge[maxn];
struct query{
    int l,r,lca,id,t;
}que[maxn];
struct Change{
    int pos,color;
}c[maxn];
int head[maxn];
int first[maxn],last[maxn],euler[maxn];
int fa[maxn],son[maxn],top[maxn],size[maxn],deep[maxn];
int book[maxn],flag[maxn];
int read()
{
    int x=0;
    char ch=getchar();
    while(ch<'0'||ch>'9')
    ch=getchar();
    while(ch<='9'&&ch>='0')
    x=x*10+ch-48,
    ch=getchar();
    return x;
}
bool cmp(query a,query b)
{
    if(a.l/block==b.l/block)
    return a.r<b.r;
    else 
    return a.l/block<b.l/block;
}
void add(int u,int v)
{
    edge[++k].to=v;
    edge[k].next=head[u];
    head[u]=k;
}
void dfs1(int u,int f)
{
    first[u]=++cnt;
    euler[cnt]=u;
    size[u]=1;
    for(int i=head[u];i;i=edge[i].next)
    {
        int v=edge[i].to;
        if(v!=f)
        {
            deep[v]=deep[u]+1;
            dfs1(v,u);
            fa[v]=u;
            size[u]+=size[v];
            if(size[son[u]]<size[v])
            son[u]=v; 
        }
    }
    last[u]=++cnt;
    euler[cnt]=u;
}
void dfs2(int u,int tp)
{
    top[u]=tp;
    if(son[u])
    dfs2(son[u],tp);
    for(int i=head[u];i;i=edge[i].next)
    {
        int v=edge[i].to;
        if(v!=fa[u]&&v!=son[u])
        dfs2(v,v);
    }
}
int lca(int u,int v)
{
    while(top[u]!=top[v])
    {
        if(deep[top[u]]<deep[top[v]])
        swap(u,v);
        u=fa[top[u]];
    }
    return deep[u]<deep[v]?u:v;
}
void update(int pos)
{
    if(flag[pos]==0){
        book[a[pos]]++;
        num+=(long long)v[a[pos]]*w[book[a[pos]]];
    }else{
        book[a[pos]]--;
        num-=(long long)v[a[pos]]*w[book[a[pos]]+1];
    }
    flag[pos]^=1;
}
void change(int tme)
{
    if(flag[c[tme].pos]==1)
    {
        book[c[tme].color]++;
        num+=(long long)v[c[tme].color]*w[book[c[tme].color]];
        book[a[c[tme].pos]]--;
        num-=(long long)v[a[c[tme].pos]]*w[book[a[c[tme].pos]]+1];
    }
    swap(c[tme].color,a[c[tme].pos]);
}
int main(){
    n=read();m=read();q=read();
    for(int i=1;i<=m;i++)
    v[i]=read();
    for(int i=1;i<=n;i++)
    w[i]=read();
    for(int i=1;i<n;i++)
    {
        int u=read(),v=read();
        add(u,v);
        add(v,u);
    }
    for(int i=1;i<=n;i++)
    a[i]=read();
    dfs1(1,1);
    dfs2(1,1);
    for(int i=1;i<=q;i++)
    {
        int opt=read(),x=read(),y=read();
        if(opt==0){
            t1++;
            c[t1].pos=x;
            c[t1].color=y;
        }else{
            t2++;
            que[t2].t=t1;
            que[t2].id=t2;
            que[t2].lca=lca(x,y);
            if(first[x]>first[y])
            swap(x,y);
            if(que[t2].lca==x){
                que[t2].l=first[x];
                que[t2].r=first[y];
                que[t2].lca=0;
            }else{
                que[t2].l=last[x];
                que[t2].r=first[y];
            }
        }
    }
    block=pow(n,2.0/3);
    sort(que+1,que+t2+1,cmp);
    for(int i=1;i<=t2;i++)
    {
        while(l<que[i].l)update(euler[l++]);
        while(l>que[i].l)update(euler[--l]);
        while(r<que[i].r)update(euler[++r]);
        while(r>que[i].r)update(euler[r--]);
        while(now<que[i].t)change(++now);
        while(now>que[i].t)change(now--);
        if(que[i].lca)update(que[i].lca);
        ans[que[i].id]=num;
        if(que[i].lca)update(que[i].lca);
    }
    for(int i=1;i<=t2;i++)
    printf("%lld\n",ans[i]);
    return 0;
}
```



