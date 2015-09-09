---
layout: post
category: Algorithm
title: Binary Tree Level Order Traversal 
tagline: by Archer
tags: [LeetCode]
---
题目: Binary Tree Level Order Traversal 
Given a binary tree, return the level order traversal of its nodes' values. (ie, from left to right, level by level).
For example:
Given binary tree {3,9,20,#,#,15,7},
    3
   / \
  9  20
    /  \
   15   7
return its level order traversal as:
[
  [3],
  [9,20],
  [15,7]
]

<!--more-->
最简单的方法就是类似bfs一样的逐层遍历，但由于要求每一层分开，所以没法使用queue来进行bfs，
使用一个currentLevel暂存当前遍历到的那一层
{% highlight java %}
	public class Solution {
    public List<List<Integer>> levelOrder(TreeNode root) {
        List<List<Integer>> returnArray = new ArrayList<List<Integer>>();
         if(root == null)
            return returnArray;
        List<TreeNode> currentLevel = new ArrayList<TreeNode>();
        //暂存当前层的结果
        currentLevel.add(root);
        while(!currentLevel.isEmpty())
        {
            List<TreeNode> nextLevel = new ArrayList<TreeNode>();
            List<Integer> currentRecord = new ArrayList<Integer>();
            
            for(TreeNode t : currentLevel)
            {
                currentRecord.add(t.val);
                if(t.left != null)
                    nextLevel.add(t.left);
                if(t.right != null)
                    nextLevel.add(t.right);
            }
            returnArray.add(currentRecord);
            currentLevel = nextLevel;
        }
        return returnArray;
    }
}
{% endhighlight %}

同样存在递归的方法，也就是一种dfs的方法，遍历到每一个元素，其实只需要记住当前元素所属的层数就可以了
另一道题目 zigzag traversal
Given a binary tree, return the zigzag level order traversal of its nodes' values. (ie, from left to right, then right to left for the next level and alternate between).
{% highlight java %}
public class Solution {
    public List<List<Integer>> zigzagLevelOrder(TreeNode root) {
        List<List<Integer>> returnList = new ArrayList<List<Integer>>();
        if(root == null)
            return returnList;
        int level = 0;
        zigzagLevelOrderRecursive(root,returnList,level);
        return returnList;
    }
    
    public void zigzagLevelOrderRecursive(TreeNode node, List<List<Integer>> returnList, int level)
    {
        if(node == null)
            return;
        if(level >= returnList.size())
            returnList.add(level,new ArrayList<Integer>());
        if(level % 2 == 0)
            returnList.get(level).add(node.val);
        else
            returnList.get(level).add(0,node.val);
        zigzagLevelOrderRecursive(node.left, returnList, level + 1);
        zigzagLevelOrderRecursive(node.right,returnList, level + 1);
    }
}


{% endhighlight %}
这里level用来纪录当前遍历到达的层数，returnList用来在递归中纪录每一层的内容，如果到达了某一层，比如level=2（从0层开始（,发现现在returnList的大小只有2，那么就应该向
returnList中添加一个子List用来纪录level2的遍历结果
总结下来其实属于前序遍历的一种