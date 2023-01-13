# 题解 P6011 【[SCOI2006]动态最值】

## 题解：
这道题是要求以下两个操作：删除序列上的一个数或查询一段区间的最值。对于这道题，我们可以用线段树求解。对于每一个节点，我们只需要记录当前节点的真实节点数量以及最值。当删除节点时，如果待删除的节点小于该节点左儿子的大小，在左子树递归求解，反之在右子树求解。当遍历到叶子节点时，将当前节点的大小修改为0，分别把极大值和极小值赋为-inf和inf，这样就可以避免答案错误。查询操作也是同理，根据子树的大小判断应该往那颗子树遍历。
## 代码：
```cpp
#include<cstdio>
#include<cstring>
#include<algorithm>
using namespace std;
const int maxn=1e6+10,inf=0x7f7f7f7f;
int n,m;
int a[maxn];
struct node{
    int _max,_min,size,l,r;
}tree[maxn*4];
void build(int id,int l,int r)
{
    tree[id].l=l,tree[id].r=r;
    if(l==r){
        tree[id]._min=tree[id]._max=a[l];
        tree[id].size=1;
        return ;
    }
    int mid=(l+r)/2;
    build(id*2,l,mid),build(id*2+1,mid+1,r);
    tree[id]._max=max(tree[id*2]._max,tree[id*2+1]._max);
    tree[id]._min=min(tree[id*2]._min,tree[id*2+1]._min);
    tree[id].size=tree[id*2].size+tree[id*2+1].size;
}
void update(int id,int x)
{
    if(tree[id].size==1){
        tree[id].size=0;
        tree[id]._min=inf;
        tree[id]._max=-inf;
        return ;
    }
    if(x<=tree[id*2].size)update(id*2,x);
    else update(id*2+1,x-tree[id*2].size);
    tree[id].size=tree[id*2].size+tree[id*2+1].size;
    tree[id]._max=max(tree[id*2]._max,tree[id*2+1]._max);
    tree[id]._min=min(tree[id*2]._min,tree[id*2+1]._min);
}
node query(int id,int l,int r)
{
    if(tree[id].size==r-l+1)return tree[id];
    if(r<=tree[id*2].size)return query(id*2,l,r);
    else if(l>tree[id*2].size)return query(id*2+1,l-tree[id*2].size,r-tree[id*2].size);
    else {
        node t1=query(id*2,l,tree[id*2].size),t2=query(id*2+1,1,r-tree[id*2].size),t3;
        t3._max=max(t1._max,t2._max),t3._min=min(t1._min,t2._min);
        return t3;
    }
}
int main(){
    scanf("%d%d",&n,&m);
    for(int i=1;i<=n;i++)
        scanf("%d",&a[i]);
    build(1,1,n);
    for(int i=1,opt,x,y;i<=m;i++)
    {
        scanf("%d",&opt);
        if(opt==1){
            scanf("%d",&x);
            update(1,x);
        }else{
            scanf("%d%d",&x,&y);
            node t=query(1,x,y);
            printf("%d %d\n",t._min,t._max);
        }
    }
    return 0;
}

```

