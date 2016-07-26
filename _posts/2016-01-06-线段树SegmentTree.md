---
layout: post
category: 面试
title: 线段树SegmentTree
tagline: by Archer
tags:
- 面试
- Lintcode

---

假设我们有一个数组a[0...n-1],在数组上我们经常进行这样两个操作

1. 求i...j(0<=i<=j<=n-1)的元素之和

2. 更新某个index上元素的值

最简单的方法是求和时每次遍历i...j，这样求和的效率是n，更新的效率是1

还可以记录一个前缀和数组，记录0...i的所有元素的和，这样求和的效率就变成了1（sum[j]-sum[i-1]），但是更新时的效率就变成了n

线段树可以做到两种操作都是o（logn）

1. 线段树的每个叶子节点都对应了Input的Array中的一个元素（也可以带有某个性质，比如线段树如果是用于表示某个区间内的最大值的，那么[3,3]这个位置的性质就是自己的值（自己的值是[3,3]内最大的）

2. 每一个非叶子节点代表了叶子节点的某种merging，对于每一个问题都不一样，本问题中代表的是sum

线段树，类似区间树，是一个完全二叉树，它在各个节点保存一条线段（数组中的一段子数组），主要用于高效解决连续区间的动态查询问题，由于二叉结构的特性，它基本能保持每个操作的复杂度为O(logn)。

线段树是一个完全二叉树（只有最后一层不满），假设数组长度为n，由于有n个叶子节点，那么内部节点的数量就是n-1(完全二叉树性质),总共节点的数量就是2n-1。对于index为i的一个节点，它的左孩子为2i+1,右孩子为2i+2。树的高度为Math.ceil(log2(n)),ceil为向上取整，当然，数组开大一点似乎也没什么关系

#####创建线段树

采用递归，左中右，就不仔细说步骤了

#####查询线段树

query也是采用递归，三个参数，当前节点，i，j

（1）如果当前节点的range在i，j之间，直接返回当前节点的值

（2）如果当前节点的range完全不在i，j之间，返回0

（3）如果当前节点有一部分落在i，j之间（交错），则返回query(左子树，i，j)+query(右子树,i,j)

我自己想到了另外一种解法，

（1）如果当前节点的range和i，j完全一样，直接返回当前节点的值

（2）取i，j的中点，如果i在中点右侧（包含i=中点情况），则query右子树，如果j在中点左侧，则query左子树

（3）否则取Math.sum（query (左子树，i，middle) + query（右子树，middle+1,j)

#####更新线段树

如果要更新index为i的节点，先去query原来的节点值，然后算出增加（减少）的diff值。之后根节点开始向下更新，只要某个节点的range包含i，就把这个节点的value增加diff。

对于线段树来说，更新这个操作对于不同的问题会有不同的解法，在这个问题中，sum只要自上而下更新就好了，但是比如对于lintcode上的Maximum Segment Tree，internal节点表示的是某一个区间内的最大值，这时就不仅仅是自上而下更新这么简单了

题目

For a Maximum Segment Tree, which each node has an extra value max to store the maximum value in this node's interval.

Implement a modify function with three parameter root, index and value to change the node's value with [start, end] = [index, index] to the new given value. Make sure after this change, every node in segment tree still has the max attribute with the correct value.

这种更新就要自下而上了(比如就把最大值更新成一个小的值，在从上而下的过程中你是不知道这个的是最大值的)，因为父亲节点的值是由子节点决定的，其实上一个sum tree问题也可以这么解决，父亲节点的值同样是由子节点决定，只不过正巧由于父亲结点的值是由所有子节点的某种和决定的，因此我们可以利用这个关系。

采用后序遍历

{% highlight java %}
/**
 * Definition of SegmentTreeNode:
 * public class SegmentTreeNode {
 *     public int start, end, max;
 *     public SegmentTreeNode left, right;
 *     public SegmentTreeNode(int start, int end, int max) {
 *         this.start = start;
 *         this.end = end;
 *         this.max = max
 *         this.left = this.right = null;
 *     }
 * }
 */
public class Solution {
    /**
     *@param root, index, value: The root of segment tree and
     *@ change the node's value with [index, index] to the new given value
     *@return: void
     */
    public void modify(SegmentTreeNode root, int index, int value) {
        // write your code here
        if(root == null)
            return;
        //Exact leaf node
        if(index == root.start && index == root.end){
                root.max = value;
                return;
        }
        //Internal node
        if(root.start <= index && root.end >= index){
            //后序
            //此处可以根据middle剪枝
            modify(root.left,index,value);
            modify(root.right,index,value);
            root.max = Math.max(root.left.max,root.right.max);
        }
    }
}
{% endhighlight %}
