---
layout: post
category: Algorithm
title: Majority Element
tagline: by Snail
tags: [LeetCode]
---
题目：
Given an array of size n, find the majority element. The majority element is the element that appears more than ⌊ n/2 ⌋ times.
<!--more-->
You may assume that the array is non-empty and the majority element always exist in the array.

1.正常想到的一般是HashMap的方法，不过这样第一遍归类后，仍需要再遍历HashMap中的key，最差情况下要多遍历将近1/2的元素，而且需要额外空间
 
2.再有一种方法是位操作
{% highlight java %}
public class Solution {
    public int majorityElement(int[] nums) {
        int result = 0;
        int count = 0;
        for(int i = 0 ; i < 32 ;  i ++)
        {
            int tempCount = 0;
            for(int j= 0 ; j < nums.length; j++)
                tempCount += ((nums[j] >> i ) & 1);
            result += (((tempCount > nums.length/2) ? 1 : 0) << i);
        }
        return result;
    }
}

{% endhighlight %}
感觉比较巧妙，对于数组中的每一个数字，按位进行统计，比如最高位，如果统计出32个元素的数组中，最高位为1的元素有17个（大与32/2=16），那么Majority Element最高位数字一定是1
如此可以算出Majority Element

3.还有一种叫做Moore's voting algorithm的算法，思想是：每次都找出一对不同的元素，从数组中删掉，直到数组为空或者只有一种元素。如果存在元素e出现频率超过半数，那么数组中最后剩下的就只有e
{% highlight java %}

 public int majorityElement(int[] num) {
        int count = 0; int major = num[0];
        for(int i:num) {
            if(count==0){ major=i; count++; }
            else if(i==major) count++;
            else count--;
        }
        return major;
    }
{% endhighlight %}
count=0时，表示没有候选元素，这时将候选元素取为当前元素，并且将count置为1
count不等于0，且当前元素等于候选元素时，count＋＋，相当于删除了一个单独的元素（不配对）
count不等于0，且当前元素不等于候选元素，count-－，相当于删除一对

当然，如果{1,2,3}这种情况，最后的结果会是错的，此时就需要进行一次扫描验证了，本题中并不需要因为一定存在Majority Element

该题目的进化版Majority Element 2：
Given an integer array of size n, find all elements that appear more than ⌊ n/3 ⌋ times. The algorithm should run in linear time and in O(1) space.

题目不再限定一定有Majority Element，并且限定了运行时间和空间，这里就只能使用Moore's voting algorithm的变体了
同样的，假设有两个candidate1,candidate2，并且用两个变量candidate1Count,candidate2Count来为candidate记数
因为一组数中最多有两个Majority Element(1/3 + 1/3)，所以，每次找出一套三个不同的元素，从数组中删掉，直到数组为空或者只有两种，只有一种元素，剩下的一定是超过1/3的那种元素
{% highlight java %}

public class Solution {
    public List<Integer> majorityElement(int[] nums) {
        ArrayList<Integer> returnList = new ArrayList<Integer>();
        Integer candidate1 = null;
        Integer candidate2 = null;
        int candidate1Count = 0;
        int candidate2Count = 0;
        for(int i = 0 ; i < nums.length ; i++)
        {
           if(candidate2 == null)
           {
               if(candidate1 == null)
                  candidate1 = nums[i];
               else
               {
                   if(candidate1 != nums[i])
                        candidate2 = nums[i];
                   else
                       candidate1Count ++;
               }
           }
           //以上是处理一下初始化两个candidate元素的情况，把这段分出来，为了后面代码的阅读方便
           
           if( candidate1 == nums[i] )
           {
                candidate1Count ++;
           }
            else if(candidate2 == nums[i] )
            {
                candidate2Count ++;
            }
            else if(candidate1Count == 0)
            {
                candidate1 = nums[i];
                candidate1Count = 1;
            }
            else if(candidate2Count ==0)
            {
                candidate2 = nums[i];
                candidate2Count = 1;
            }
            else{
                candidate1Count --;
                candidate2Count --;
            }
        }
        candidate1Count = 0;
        candidate2Count = 0;
        for(int i = 0 ; i < nums.length ; i++)
        {
            if(nums[i] == candidate1)
                candidate1Count ++;
            else if(nums[i] == candidate2)
                candidate2Count ++;
        }
        if(candidate1Count > nums.length /3)
        returnList.add(candidate1);
        if(candidate2Count > nums.length /3)
        returnList.add(candidate2);
        //检测是否是真的
        return returnList;
    }
}

{% endhighlight %}
