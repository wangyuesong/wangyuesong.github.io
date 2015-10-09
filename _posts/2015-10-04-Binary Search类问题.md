---
layout: post
category: Leetcode
title: Binary Search类问题总结
tagline: by Archer
tags:
- Lintcode
- LeetCode
- 九章算法

---
这一节课因为时间记错了没听！SHIT，还好讲的应该不是很难，看了一下ppt，主要讲的是binary search二分法的模板

模板如下:

{% highlight java %}
class Solution {
    /**
     * @param nums: The integer array.
     * @param target: Target to find.
     * @return: The first position of target. Position starts from 0.
     */
    public int binarySearch(int[] nums, int target) {
        if (nums == null || nums.length == 0) {
            return -1;
        }
        
        int start = 0, end = nums.length - 1;
        //这个循环结束时，start和end应该只差1，相当于只剩下两个元素
        while (start + 1 < end) {
            int mid = start + (end - start) / 2;
            if (nums[mid] == target) {
            	//注意，找到了之后还不能结束，因为可能存在重复，如果想找到第一个出现的target元素，就把end设置到mid，找最后一个就把start设置到mid
                end = mid;  
            } else if (nums[mid] < target) {
                start = mid;
            } else {
                end = mid;
            }
        }
        //这俩元素都有可能是答案，都测试一下
        if (nums[start] == target) {
            return start;
        }
        if (nums[end] == target) {
            return end;
        }
        return -1;
    }
}
{% endhighlight %}

之前一直使用的是递归的方法，其实比较浪费空间，而且没有一个像样的模板，还没有注意过有重复情况的问题，这个模板一定要背熟

