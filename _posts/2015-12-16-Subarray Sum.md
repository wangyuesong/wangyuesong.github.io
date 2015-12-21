---
layout: post
category: Lintcode
title: Subarray Sum
tagline: by Archer
tags:
- Lintcode
- LeetCode
- 九章算法

---

Array相关的问题有很多，SubArray是一大类

首先是这道题Subarray Sum

#### Given an integer array, find a subarray where the sum of numbers is zero. Your code should return the index of the first number and the index of the last number.


这里要用到前缀和的概念，前缀和可以表示为一个数组，prefixSum

prefixSum[i]表示正式数组中0...i个位置所有元素之和

一个subarray必然是连续的，并且subArray的sum为0的话，前缀和会有一个特征

举个例子：假设i...j的subArray的sum为0，我们可以知道的是prefixSum[i-1] == prefixSum[j]
3 1 -3 2, prefixSum[0] = prefixSum[2]

因为有-1的存在，所以prefixSum数组要有一个-1的空位，这个空位可以填入0

如果想要找到两个相等的prefixSum的index，当然可以每次产生一个新的index都和之前的index进行一下match，不过这样的效率是O(n2)

更好的办法是使用一个HashMap，以prefixSum的value作key，index做value，这样每次产生新key，只要去查找hashMap中是否存在这个key就可以了，查找效率为1，整体效率O(n)

代码如下：
{% highlight java %}
public class Solution {
    /**
     * @param nums: A list of integers
     * @return: A list of integers includes the index of the first number 
     *          and the index of the last number
     */
    public ArrayList<Integer> subarraySum(int[] nums) {
        // write your code here
       //关键在于想到Subarray sum为0的性质，
       //从index为0到sum为0的subarray开头 的 sum
       //和从index为0到sum为0的subarray的结尾 的 sum 
       //是一样的！！！
       HashMap<Integer,Integer> records = new HashMap<Integer, Integer>();
       int sum = 0;
       int step = 0;
       records.put(0,-1);
       while(step != nums.length){
           sum += nums[step];
           if(records.containsKey(sum)){
               ArrayList<Integer> returnValue = new ArrayList<Integer>();
               returnValue.add(records.get(sum) + 1);
               returnValue.add(step);
               return returnValue;
           }
           records.put(sum,step);
           step ++;
       }
       return new ArrayList<Integer>();
}
}
{% endhighlight %}

下面一题是本题升级版

#### Subarray closest:Given an integer array, find a subarray with sum closest to zero. Return the indexes of the first number and last number.

这一题同样要记录prefixSum，由于不仅仅是要找相同的prefixSum，所以hashMap已经不适用了，将prefixSum记录到一个数组中

要寻找最closeto zero的subArray，可以将prefixSum数组排序，最小的prefixSum间隔一定出现在排序后的prefixSum数组的某两个元素之间

但是不使用hashMap就没法记录index，而在这里在给prefixSum排序的过程中也需要将得到该prefixSum的位置一并记录下来，因此引入Pair这个数据结构，并且让它implements Comparable接口

prefixSum数组中要有(0,-1)这样一个初始元素

排序后，从prefixSum数组的第2个（下标1）元素开始，每个元素和之前一个元素做减法，更新global最小值和global index pair

得到global pair后，有一个问题是有可能产生这样的结果： （3，1），因为closest to zero是不分正负的，所以我们要对这个排序之后，再在第一个index上加1得到最后的结果

{% highlight java %}
public class Solution {
    /**
     * @param nums: A list of integers
     * @return: A list of integers includes the index of the first number 
     *          and the index of the last number
     */
    private class Pair implements Comparable{
        public int index;
        public int sum;
        public Pair(int index, int sum){
            this.index = index;
            this.sum = sum;
        }
        @Override
       public int compareTo(Object obj){
            return this.sum - ((Pair)obj).sum;
        }
        
    }
    public int[] subarraySumClosest(int[] nums) {
        Pair[] sumRecords = new Pair[nums.length + 1];
        sumRecords[0] = new Pair(-1,0);
        int prev = 0;
        for(int i = 0; i < nums.length; i ++){
            sumRecords[i+1] = new Pair(i, prev + nums[i]);
            prev = prev + nums[i];
        }
        Arrays.sort(sumRecords);
        int closest = Integer.MAX_VALUE;
        int[] returnValue = new int[]{0,0};
        for(int i = 1; i< sumRecords.length ; i ++){
            if(sumRecords[i].sum - sumRecords[i-1].sum < closest){
                closest = sumRecords[i].sum - sumRecords[i-1].sum;
                int[] result = new int[]{sumRecords[i].index,sumRecords[i-1].index};
                Arrays.sort(result);
                result[0] = result[0] + 1;
                returnValue = result;
            }
        }
        return returnValue;
    }    
}

{% endhighlight %}

下一道题，maximum subarray,找到一个array中最大的subArray

同样要用到prefixSum，同样需要O(n)效率

与之前有一些不同，prefixSum这次不用存储了，我们可以记录三个全局变量，一个是max（代表走到当前位置的所有subArray的最大值，这个就是要返回的值），另一个是sum，记录到当前位置的所有元素的和，
另一个是prefixMin,这个用来记录到当前位置，前缀和的最小值在是多少。这样，每次我们走到一个位置，

(1)更新sum为sum+currentValue

(2)更新max为max和sum-prefixMin的最大值

(3)更新prefixMin为prefixMin和prefixMin + currentValue的最小值

这样最后直接返回max就可以了

当然也可以存两个数组，一个数组存sum一个数组寸prefixMin，遍历第一遍之后再重新返回遍历一边，求得sum-prefixMin的最小值

{% highlight java %}
public class Solution {
    /**
     * @param nums: A list of integers
     * @return: A integer indicate the sum of max subarray
     */
    public int maxSubArray(int[] nums) {
       if(nums.length == 1)
           return nums[0];
        // sums[0] = 0;
      
        int currentSum = 0;
        int i = 0;
        int max = Integer.MIN_VALUE;
        int prefixMin = 0;
        //在找CurrentSum过程中可以记录前缀的最小和
        while(i != nums.length){
            //加第0个
            currentSum += nums[i];
            //当前位置和减去当前位置之前的前缀和最小值
            max = Math.max(max,currentSum - prefixMin);
            //更新前缀和
            prefixMin = Math.min(prefixMin, currentSum);
            // max = Math.max(currentSum, max);
            // sums[i] = currentSum;
            i ++;
        }
        return max;
        
    }
}
{% endhighlight %}
