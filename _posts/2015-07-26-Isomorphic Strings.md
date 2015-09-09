---
layout: post
category: Algorithm
title: Isomorphic Strings
tagline: by Snail
tags: [LeetCode]
---
题目：
Given two strings s and t, determine if they are isomorphic.
<!--more-->
Two strings are isomorphic if the characters in s can be replaced to get t.

All occurrences of a character must be replaced with another character while preserving the order of characters. No two characters may map to the same character but a character may map to itself.

For example,
Given "egg", "add", return true

Given "foo", "bar", return false.

Given "paper", "title", return true.

Note
You may assume both s and t have the same length.

{% highlight java %}

public class Solution {
    public boolean isIsomorphic(String s, String t) {
        if(s == null || t == null)
            return false;
        HashMap<Character, Integer> hash1 = new HashMap<Character, Integer>();
        HashMap<Character, Integer> hash2 = new HashMap<Character, Integer>();
        
        for(int count = 0 ; count < s.length() ;  count ++)
        {
            Character char1 = s.charAt(count);
            Character char2 = t.charAt(count);
            //都是新的
            if(!hash1.containsKey(char1) && !hash2.containsKey(char2))
            {
                hash1.put(char1, count);
                hash2.put(char2, count);
                continue;
            }
            //String1中存在之前出现过的元素而String2中不存在，反之亦然，都是错的
            else if(hash1.containsKey(char1) && !hash2.containsKey(char2))
                return false;
            else if(!hash1.containsKey(char1) && hash2.containsKey(char2))
                return false;
            //两个String中都存在，就是位置不一样
            else if(hash1.get(char1) != hash2.get(char2))
                return false;
            else
                continue;
        }
        return true;
    }
}
{% endhighlight %}

用HashTable来纪录字母及其出现的位置
如果一个String中出现了一个新的字母，比如egg 和 add中， egg的第二个字母g第一次出现（hashMap.containsKey),那么就检查一下
add中的第二个字母d是否时第一次出现，如果是，则存入map中，如果不是，直接返回false
如果一个String中出现了一个已经出现的字母，之前存入的字母位置就发挥作用了，可以检测两个String中该字母对的上一次出现位置是否一样