---
layout: post
category: Lintcode
title: 并查集UnionFind
tagline: by Archer
tags:
- Union Find
- Lintcode

---

并查集主要用来解决无向图动态连通性问题(也可以用来解决无向图中的环问题)，使用union-find算法解决的问题包括给定两个点判断是否联通，如果联通不需要给出具体路径的问题，如果需要具体路径，则应该使用dfs这样的算法

直观的想法是，对于联通的节点，我们认为他们属于一个组。为了简单起见我们将所有的节点以整数表示，在一开始各个节点一定是分离的

{% highlight java %}

for(int i = 0; i < size; i++)  
    id[i] = i;    

{% endhighlight %}

可以设计如下API：

#####查询节点所属于的组 find(int p)

#####判断两个节点是否属于同一个组 connected(int p ,int q)

#####连接连个节点 union(int p, int q)

#####获取组的数目 count()

##### Quick-find

第一种Quick Find算法，find的时间效率为o（1）只要返回节点对应组号就可以了

{% highlight java %}
public class UF  
{  
    private int[] id; // access to component id (site indexed)  
    private int count; // number of components  
    public UF(int N)  
    {  
        // Initialize component id array.  
        count = N;  
        id = new int[N];  
        for (int i = 0; i < N; i++)  
            id[i] = i;  
    }  
    public int count()  
    { return count; }  
    public boolean connected(int p, int q)  
    { return find(p) == find(q); }  
    public int find(int p)  
    { return id[p]; }  
    public void union(int p, int q)  
    {   
        // 获得p和q的组号  
        int pID = find(p);  
        int qID = find(q);  
        // 如果两个组号相等，直接返回  
        if (pID == qID) return;  
        // 遍历一次，改变组号使他们属于一个组  
        for (int i = 0; i < id.length; i++)  
            if (id[i] == pID) id[i] = qID;  
        count--;  
    }  
}  

{% endhighlight %}

###### Quick- Union
但是union得效率比较低，为n，如果要添加m个union，节点数量为n，则复杂度为mn

仔细思考，union费时的原因就在于各个节点所属的组是各自为政的，同一个组的节点之间除了组号相同并没有任何联系，如果能把相同组的节点用某种结构联合起来，那么效率会变高。

将一系列节点连接起来的数据结构有链表，但是如果通过链表遍历来找两个节点所属的组的话，效率比较低，find的最坏情况为一个组内所有元素的长度

另一种数据结构可以用，那就是树，这里面用到的树和一般的二叉树不同。这个树的实现是自下而上的，id[i]中存放的是i节点父节点的index，root节点的父节点是自己，在这样的算法下，find的复杂度变为树高。

union的算法则是查找两棵树的root节点，如果不一样就把一颗树变成另一个棵的子树，这里面的子树添加很容易实现，因为树的实现自下而上，只需要把i子树的id[i] = j(j为另一棵树的root节点)就可以了

union的复杂度变为树高

###### Weighted Quick - Union

树很可能退化成链表，因为合并树时并没有考虑两颗树的高度。在合并时，应该讲小树合并到大树才对

我们可以用另一个数组size[]来记录树的大小，注意，不是高度，是整个树的大小（也就是子元素的个数）。初始化所有都为1。

在合并时首先会判断两棵树的大小，然后进行合并

{% highlight java %}
public void union(int p, int q)  
{  
    int i = find(p);  
    int j = find(q);  
    if (i == j) return;  
    // 将小树作为大树的子树  
    if (sz[i] < sz[j]) { id[i] = j; sz[j] += sz[i]; }  
    else { id[j] = i; sz[i] += sz[j]; }  
    count--;  
}  
{% endhighlight %}


###### Weighted Quick-Union with path compression

根据观察，如果想要find的时间消耗最少，我们希望所有的节点都能够距离根节点height为1，也就是一颗扁平的树结构。

想要达到这一个目标，我们在find时可以直接将所有路过的节点记录下来，最后统一把这些节点的父节点指向root，但是这样的话，find操作的频繁会经常生成子数组，造成不必要的开销

更好的方法，是在while过程中，将节点的父亲节点指向爷爷节点，甚至爷爷爷节点，由于root节点的父节点还是自己，因此不必担心溢出等情况。

{% highlight java %}
private int find(int p)  
{  
    while (p != id[p])  
    {  
        // 将p节点的父节点设置为它的爷爷节点  
        id[p] = id[id[p]];  
        p = id[p];  
    }  
    return p;  
}  
{% endhighlight %}

这种算法下，find和union的效率都接近1（因为树高很小）

只要在find时加这一句话！！！
 groups[i] = groups[groups[i]];
