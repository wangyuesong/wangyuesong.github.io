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
### Given two words word1 and word2, find the minimum number of steps required to convert word1 to word2. (each operation is counted as 1 step.)

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



### Given a string S and a string T, count the number of distinct subsequences of T in S.

A subsequence of a string is a new string which is formed from the original string by deleting some (can be none) of the characters without disturbing the relative positions of the remaining characters. (ie, "ACE" is a subsequence of "ABCDE" while "AEC" is not).

题目的意思是从S中有多少种不同的子序列能够构成T，其实题目说的不是很清楚，并不是说S和T有多少公共的子序列

递推关系不是很明确，但是可以肯定是一个双字符串动态规划问题，dp的内容存的应该是:

dp[i][j] = 一共有多少种方法能够用s中前j个字符组成t的前i个字符

dp的大小应该是(t.length() + 1) X (s.length() + 1)，把空串也算进去

初始化j=0的第一列表示空串到t的前i个字符有多少种转换方式，全是1

初始化i=0的第一行表示s的前j个字符到空串有多少种转换方式，当然全都是1

递推的关系需要想一想，这样的题一般都是比较第i，j个位置的字符是否相等，根据是否相等的结果来根据之前的dp的结果来递推到这个dp的结果

由于dp开的大小是t.length()+1,s.length()+1，所以我们如果比较字符串中的字符的话应该是取t.charAt(i-1),s.charAt(j-1)。暂时先不考虑这两个字符相不相等，即使他们不相等，dp[i][j]的结果也至少是dp[i][j-1],表示不考虑这一位，退一步让s的前j-1和t的前i个字符匹配。

如果两个不相等的话，dp[i][j]就是dp[i][j-1]。如果相等的话则可以让这两个字符匹配，然后让s的前j-1个和t的前i-1个相匹配，

{% highlight java %}
dp[i][j] = dp[i][j-1] + s.charAt(j-1) == t.charAt(i-1) ? dp[i-1][j-1] : 0;
{% endhighlight %}

代码如下：
{% highlight java %}
public class Solution {
    /**
     * @param S, T: Two string.
     * @return: Count the number of distinct subsequences
     */
    public int numDistinct(String S, String T) {
        // write your code here
        int[][] dp = new int[T.length()+1][S.length()+1];
        for(int i = 0; i < dp.length; i ++)
        // S is empty string
            dp[i][0] = 0;
        for(int j = 0; j < dp[0].length; j ++)
        // T is empty string
            dp[0][j] = 1;
        //i for T , j for S
        for(int i = 1; i < dp.length; i ++)
            for(int j = 1; j < dp[0].length; j ++)
            {
                dp[i][j] = dp[i][j-1] + (S.charAt(j-1) == T.charAt(i-1)?dp[i-1][j-1]:0);
            }
        return dp[T.length()][S.length()];
    }
}

{% endhighlight %}

### Longest common subsequence and Longest common substring

最长公共子序列和最长公共字串，都是二维动态规划问题，状态表中由于第i行只用到了第i-1行的东西，可以使用滚动数组进行优化

最长公共子序列的递推关系为：
{% highlight java %}
if(s[i-1] == s[j-1])
	f[i][j] = f[i][j] + 1;
else
	Max(f[i-1][j],f[i][j-1]);
{% endhighlight %}
至今没有想明白的是common string的解法，我想的是
{% highlight java %}
dp[i][j] = 
if(s[i-1] != t[j-1]])
	Max(dp[i-1][j],dp[i][j-1]);
else
	if(i-2,j-2 is valid and s[i-2] == t[j-2])
		dp[i-1][j-1]+1
	else
		Max(1,dp[i-1][j-1])
{% endhighlight %}
答案则将dp[i][j] 考虑为以i，j结尾的字符串的的公共字串，答案就相当简单，我目前还没有想好为什么我的想法是错的
