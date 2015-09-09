---
layout: post
category: Algorithm
title: Reverse Linked List
tagline: by Archer
tags: [LeetCode]
---
题目: Reversed a singly linked list
<!--more-->
{% highlight java %}
	public class Solution {
    public ListNode reverseList(ListNode head) {
        if(head == null)
            return null;
        ListNode returnHead = head;
        while(returnHead.next != null)
            returnHead = returnHead.next;
        iterRerverseList(head);
        return returnHead;
    }
    
    public void recurReverseList(ListNode node, ListNode previous)
    {
        if(node == null)
            return;
        recurReverseList(node.next,node);
        node.next = previous;
    }
    
    public void iterRerverseList(ListNode node)
    {
        ListNode previousNode = null;
        ListNode currentNode = node;
        while(currentNode != null)
        {
            ListNode tempNode = nextNode.next;
            currentNode.next = previousNode;
            previousNode = currentNode;
            currentNode = tempNode;
        }
    }
}
{% endhighlight %}

递归和不递归两种
递归需要先走到链表的最后，然后从后向前一个个调转next指针
非递归循环中有三个node，current纪录当前的，previous纪录之前的，tempNode用来纪录curent的下一个节点
，因为在扭转current的next指针的时候，会失去current的下一个节点的饮用
  
看了一个 别人的方法，递归中可以只传递一个参数，第一遍从头走到尾，把所有的指针全部拆掉，
返回时再从尾到头把指针全部反向接上

{% highlight java %}

public class Solution {     
     public ListNode reverseList(ListNode head) {         
             if(head == null || head.next == null)             
                     return head;         
             ListNode second = head.next;        
             head.next = null;         
            ListNode newHead = reverseList(second);         
            second.next = head;         
            return newHead;     
        } 
     }

{% endhighlight %}