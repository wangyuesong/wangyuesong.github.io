---
layout: post
category: Leetcode
title: Two Sequence动态规划问题
tagline: by Archer
tags:
- Lintcode
- LeetCode
- 九章算法

---
Given two words word1 and word2, find the minimum number of steps required to convert word1 to word2. (each operation is counted as 1 step.)

You have the following 3 operations permitted on a word:

Insert a character

Delete a character

Replace a character


{% highlight java %}

public class Solution {
    /**
     * @param word1 & word2: Two string.
     * @return: The minimum number of steps.
     */
    public int minDistance(String word1, String word2) {
        // write your code here
        int[][] f = new int[word1.length() + 1][word2.length() + 1];
        for(int i = 0; i < f.length; i ++)
            f[i][0] = i;
        for(int j = 0 ; j < f[0].length; j ++)
            f[0][j] = j;
        for(int i = 1 ;i < f.length ; i++)
            for(int j = 1; j < f[0].length; j ++)
            {
                int minStepForIJ = Integer.MAX_VALUE;
                if(word1.charAt(i-1) == word2.charAt(j-1))
                //f[i-1][j] means delete from i
                //f[i][j-1] means add one charaacter(last c in b) to first string
                    minStepForIJ = Math.min( f[i][j-1]+1,Math.min(f[i-1][j-1],f[i-1][j] + 1));
                else
                    minStepForIJ = Math.min( f[i][j-1]+1,Math.min(f[i-1][j-1] + 1,f[i-1][j] + 1));
                f[i][j] = minStepForIJ;
            }
        return f[f.length-1][f[0].length-1];       
    }
}


{% endhighlight %}

两字符串比较的问题一般都会开个二维数组，f[i][j]表示的是第一个字符串的前i个字符和第二个字符串的前j个字符之间的关系。因此，把前0个字符也算作一个比较位，f应该开成int[a.length()+1][b.length()+1]的一个数组

还要注意这里
{% highlight java %}
if(word1.charAt(i-1) == word2.charAt(j-1))
{% endhighlight %}

举例，当i和j都是1的时候，我们比较的是a的前1个字符和j的前1个字符之间的关系，第一个字符出现的位置其实是0，所以要用i-1，j-1

当a.charAt(i-1) != b.charAt(j-1)时，我们可以通过三种方法把a变成b
 
(1)在a的末尾增加b的末尾的字符，这样a，b两个字符串的末尾就匹配完成了，剩下的就是a字符串的前i个和b字符串的前j-1个字符进行匹配, f[i][j-1] + 1

(2)把a的末尾字符删除，f[i-1][j] + 1

(3)a的末尾字符替换, 这时a，b两个字符串的末尾肯定是匹配的了，f[i-1][j-1] + 1

