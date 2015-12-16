---
layout: post
category: Lintcode
title: Merge k sorted lists
tagline: by Archer
tags:
- Lintcode
- LeetCode
- 九章算法

---

###Merge k Sorted Lists Show result 

Merge k sorted linked lists and return it as one sorted list.

Analyze and describe its complexity.

可以使用divide and conquer法来解，假设两个子list都已经被sorted了，在主方法里进行两个有序list的merge

需要注意的是from，to的的边界以及from==to时的最小问题情况

代码如下：
{% highlight java %}
private ListNode mergeHelper(List<ListNode> lists,int from, int to){
        if(from > to)  
            return null;
        if(from == to)
            return lists.get(from);
            
        ListNode leftMergedList = mergeHelper(lists, from, (from+to)/2);
        ListNode rightMergedList = mergeHelper(lists, (from+to)/2+1, to);
        return mergeTwoList(leftMergedList,rightMergedList);
    }
    
    private ListNode mergeTwoList(ListNode left, ListNode right){
        ListNode dummy = new ListNode(0);
        ListNode current = dummy;
        while(left != null && right != null){
            if(left.val >= right.val){
                current.next = right;
                right = right.next;
                current = current.next;
            }
            else
            {
                current.next = left;
                left = left.next;
                current = current.next;
            }
        }
        if(right == null)
            current.next = left;
        else
            current.next = right;
        return dummy.next;
    }
{% endhighlight %}

比较新的方法是使用Priortiy Queue，因为在一开始我就想过使用HashMap<ListNode,Integer>这样的方法，但是这种方法假设有k条list，每次merge一个元素进最终list都要进行k次查找，并且纵然知道了当前所有list中最小的integer大小，也不能对应到其ListNode节点上来

Priority Queue是Queue的一种实现，Java的Priority Queue是基于最大堆实现的

复习一下

1. 堆的实现一般基于数组，堆中的一个元素一定会大于其左右树中的元素，但是左右树中元素的大小关系是不确定的。 

2. 一个下标为n的元素，左右儿子的位置在2n+1,2n+2,父亲的位置在(n-1)/2

3. 堆首先将元素堆入数组，然后开始构建堆，构建堆的动作可以通过元素一一插入堆来实现，效率为nLogn(每次插入都要进行LogN的调整)。也可以通过递归的方法，逐层由下向上建立Heap，耗时为n

PriorityQueue其add的效率为O(logN),因为每次增加都是将元素加入堆底，然后进行adjust

poll（取最大）的效率也为O(logN),同样是因为要进行调整

remove（删除某个元素）和contains效率为n，因为要搜索两侧

peek，element等方法速度为1，不需要进行调整

还有Queue的一系列方法的区别

offer/add, poll/remove, peek/element,所有pair的左侧方法都是不抛出异常，右侧抛出

代码如下：
{% hightlight java %}
public ListNode mergeKLists(List<ListNode> lists) {  
        // write your code here
        // return mergeHelper(lists, 0, lists.size()-1);
        Comparator<ListNode> listNodeComparator = new Comparator<ListNode>(){
            public int compare(ListNode left, ListNode right){
                // if(left == null)
                //     return 1;
                // if(right == null)
                //     return -1;
                return left.val - right.val;
            }
        };
        if(lists == null || lists.size() == 0)
            return null;
        PriorityQueue<ListNode> pq = new PriorityQueue<ListNode>(lists.size(), listNodeComparator);
        for(ListNode node:lists){
            if(node != null)
              pq.add(node);
        }
        ListNode dummy = new ListNode(0);
        ListNode current = dummy;
        while(!pq.isEmpty()){
            ListNode topNode = pq.poll();
            current.next = topNode;
            current = current.next;
            if(topNode.next != null)
                pq.add(topNode.next);
        }
        return dummy.next;
    }
{% endhighlight %}

要注意Comparator的写法！
