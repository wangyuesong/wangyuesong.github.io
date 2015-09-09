---
layout: post
category: Algorithm
title: Course Schedule
tagline: by Archer
tags: [LeetCode]
---
题目: There are a total of n courses you have to take, labeled from 0 to n - 1

Some courses may have prerequisites, for example to take course 0 you have to first take course 1, which is expressed as a pair: [0,1]

Given the total number of courses and a list of prerequisite pairs, is it possible for you to finish all courses?
<!--more-->

拓扑排序：将有向图中的顶点以线性方式进行排序。即对于任何连接自顶点u到顶点v的有向边uv，在最后的排序结果中，顶点u总是在顶点v的前面。
并不是所有的有向图都能进行拓扑排序，一个有向图能够被拓扑排序的充要条件是他是一个有向无环图
有向无环图必然满足偏序关系（两个元素之间的关系要么确定，要么不确定，绝对不存在矛盾关系，即环路）

实在是太傻逼了，这个题写了很久，感觉都对，就是有几个case一直错，太困了，看了别人的答案，思路基本相同，就是一些小的细节不太一样，写了一个自己的
{% highlight java %}
	public class Solution {
    public boolean canFinish(int numCourses, int[][] prerequisites) {
         List<List<Integer>> adjacencyList = new ArrayList<List<Integer>>();
         initAdjacencyList(numCourses, prerequisites, adjacencyList);
         boolean isVisited[] = new boolean[numCourses];
         for(int i = 0; i < numCourses; i ++)
         {
             boolean[] trace = new boolean[numCourses];
             if(hasCycle(i, adjacencyList, isVisited, trace))
                return false;
         }
         return true;
    }
    
    public void initAdjacencyList(int numCourses, int[][] prerequisites,  List<List<Integer>> adjacencyList)
    {
        for(int i = 0 ; i < numCourses; i ++)
            adjacencyList.add(new ArrayList<Integer>());
        for(int i = 0 ; i < prerequisites.length; i ++)
            adjacencyList.get(prerequisites[i][0]).add(prerequisites[i][1]);
    }
    
    public boolean hasCycle(int currentCourse, List<List<Integer>> adjacencyList, boolean[] isVisited, boolean[] trace)
    {
        if(isVisited[currentCourse])
            return false;
        if(trace[currentCourse])
            return true;
       
        trace[currentCourse] = true;
        boolean result = false;
        for(int adjCourse: adjacencyList.get(currentCourse))
             if(hasCycle(adjCourse, adjacencyList, isVisited, trace))
                return true;
        isVisited[currentCourse] = true;
        //深度优先！将visit操作放在最后
        return false;
    }
    
}
{% endhighlight %}
根据prerequisites初始化一个AdjacencyList
题目其实就是要检测这个有向图是否存在环
boolean[] trace用于递归中纪录递归的路径，检测其是否成环
boolean[] isVisited用于DFS
trace和isVisited的区别在于，trace在每一次从一个新的节点开始一个DFS路径时，都会被重新新建
而在这次DFS路径中，trace会被慢慢填充，在这次路径中，trace中如果有重复则一定是环，因此
  
	trace[currentCourse] = true;
  
一定要在开始递归之前，将本节点加入trace中，以便后面的递归算法检测是否有环

而

	isVisited[currentCourse] = true;
  
则应该放在递归算法的最后，因为如果存在1->2->3->1
这样一个环，从1开始递归，如果将这一句话放在前面，则这个环无法被检测出来

