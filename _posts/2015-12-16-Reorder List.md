---
layout: post
category: Lintcode
title: Reorder List
tagline: by Archer
tags:
- Lintcode
- LeetCode
- 九章算法

---

Given a singly linked list L: L0→L1→…→Ln-1→Ln,
reorder it to: L0→Ln→L1→Ln-1→L2→Ln-2→…

You must do this in-place without altering the nodes' values.

zigzag 重排list，思路是找到中点，然后inverse后半段的list，之后一个从head，一个从tail串起来

分为几个函数findMiddle, reverse

使用双指针的方式获取middle位置，此处middle位置是偏右的，如果有5个元素会取得第三个，如果有4个元素同样会取得第三个

之后再从middle处调用reverse来翻转list，reverse函数要写熟，最后返回prev

要注意此时前半段的list的尾部仍然和middle连在一起，可以先查找到连接点把连接断开

串起的动作有些麻烦，可以画一个图来演示一下，注意当head==null时就可以结束循环了，此时tail剩余最多一个（因为前后半段的list最多差1），不需要再做任何操作，list就已经reorder好了

代码如下：
{% highlight java %}
public class Solution {
    /**
     * @param head: The head of linked list.
     * @return: void
     */
    public void reorderList(ListNode head) { 
        if(head == null || head.next == null)
            return;
         // write your code here
        ListNode record = head;
        ListNode middle = findMiddle(head);
        ListNode tail = reverseList(middle);
        while(head != null && head.next != middle){
            head = head.next;
        }
        //Break the chain
        head.next = null;
        head = record;
        
        while(head != null && tail != null){
            ListNode temp = head.next;
            head.next = tail;
            head = temp;
            if(head == null)
                break;
            temp = tail.next;
            tail.next = head;
            tail = temp;
        }
                
    }
    
    private ListNode findMiddle(ListNode head){
        ListNode faster = head;
        while(faster != null && faster.next != null){
            head = head.next;
            faster = faster.next.next;
        }
        return head;
    }
    private ListNode reverseList(ListNode head){
        ListNode prev = null;
        while(head != null){
            ListNode temp = head.next;
            head.next = prev;
            prev = head;
            head = temp;
        }
        return prev;
    }
}

{% endhighlight %}



