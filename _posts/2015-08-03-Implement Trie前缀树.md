---
layout: post
category: Algorithm
title: Implements Trie 前缀树 
tagline: by Archer
tags: [LeetCode]
---
题目：Implement a trie with insert, search, and startsWith methods.

Note:
You may assume that all inputs are consist of lowercase letters a-z.
<!--more-->

定义：
	所有含有公共前缀的字符串将挂在树中同一个结点下。实际上trie简明的存储了存在于串集合中的所有公共前缀。
![Yanghu](/assets/2015-08-03-Implement Trie前缀树/IMG_1267.gif)
这里会遇到一个问题，那就是如果这些将要存储的字符串中，有一个是另一个的前缀，比如bi和big，那么在一个枝干上将出现两个叶子节点，这是不符合定义的
	
为了避免这个问题，我们在每个字符串后面加上一个$,这样就不存在互为前缀的字符串了

实现思路：
TrieNode于Trie两个数据结构

(1)TrieNode包含一个Character，表示这个节点存储的字符，以及用HashMap<Character, TrieNode>来表示的孩子集，表示这个TrieNode的所有子节点

(2)Trie包含一个为空的root节点

###Insert：
递归插入，在插入之前，给每个字符串后缀$
要注意

	if(!currentNode.children.keySet().contains(currentCharacter))
		currentNode.children.put(currentCharacter, new TrieNode(currentCharacter));
		
当两个字符串的前缀有交集之时，直接利用之前建立的节点即可

###search(word)：
在这里刻意采用递归，在开始写代码之前，应该想好这个情况

当要查找的word 对比到了最后一个字母时，比如查找abc，现在递归到了c这个位置，此时应该检查该节点是否是一个叶子节点， 而叶子节点的标示就是
有一个$的子节点，如果是有的话，则查找成功，如果不是叶子节点，证明这只是某个其他字符串的前缀，实际树中并没有存储这个字符串，查找失败

###startWith(word)
这里不再采用递归的方式，总的来说思路比较简单，就是一个一个的比较下来，search如果不用递归的话，实现起来也是比较简单

代码：

{% highlight java %}

class TrieNode {
    // Initialize your data structure here.
    Character c;
    HashMap<Character, TrieNode> children;
    public TrieNode() {
        c = null;
        children = new HashMap<Character, TrieNode>();
    }
    
    public TrieNode(Character c)
   
        this.c = c;
        children = new HashMap<Character ,TrieNode>();
    }
}

public class Trie {
    private TrieNode root;

    public Trie() {
        root = new TrieNode();
    }

    // Inserts a word into the trie.
    public void insert(String word) {
        recursiveInsert(word + "$", 0, root);
    }
    
    private void recursiveInsert(String word, int position, TrieNode currentNode)
    {
        if(word.length() <= position)
        {
            return;
        }
        Character currentCharacter = word.charAt(position);
        if(!currentNode.children.keySet().contains(currentCharacter))
            currentNode.children.put(currentCharacter, new TrieNode(currentCharacter));
        recursiveInsert(word, position + 1, currentNode.children.get(currentCharacter));
    }

    // Returns if the word is in the trie.
    public boolean search(String word) {
        return recursiveSearch(word, 0, root);
    }
    
    private boolean recursiveSearch(String word, int currentPosition, TrieNode currentNode)
    {
          
          if(currentPosition >= word.length())
          //长度达到，结束查找
            {
                if(currentNode.children.keySet().contains('$'))  
                    return true;
                //结束于某个叶子节点
                else
                    return false;
                //没有结束于叶子节点
            }
        Character currentCharacter = word.charAt(currentPosition);
        if(currentNode.children.get(currentCharacter)!= null)
           return recursiveSearch(word, currentPosition + 1, currentNode.children.get(currentCharacter));
        else
           return false;
    }

    // Returns if there is any word in the trie
    // that starts with the given prefix.
    public boolean startsWith(String prefix) {
        TrieNode currentNode = root;
        int position = 0;
        while(position < prefix.length())
        {
            if(currentNode.children.keySet().contains(prefix.charAt(position)))
            {
                currentNode = currentNode.children.get(prefix.charAt(position));
                position++;
            }
            //查找到非叶节点，prefix匹配成功，继续匹配
            else
                return false;
            //prefix匹配失败，退出
        }
        return true;
    }
    
}

// Your Trie object will be instantiated and called as such:
// Trie trie = new Trie();
// trie.insert("somestring");
// trie.search("key");
{% endhighlight %}
后来发现别人的解法要比我的这个简单多了，TrieNode的实现采用数组而不是Map的方式，感觉这样更加直观一些

{% highlight java %}

class TrieNode {
    public TrieNode[] child_nodes;
    public char node_char;
    public int freq;

    public TrieNode() {
        child_nodes = new TrieNode[26];
        node_char = 0;
        freq = 0;
    }
}

public class Trie {
    private TrieNode root;

    public Trie() {
        root = new TrieNode();
    }

    public void insert(String word) {
        TrieNode child = root;

        for(int i=0; i< word.length(); i++) {
            int index = word.charAt(i) - 'a';
            if(child.child_nodes[index] == null) child.child_nodes[index] = new TrieNode();
            child = child.child_nodes[index];
            if(child.node_char == 0) child.node_char = word.charAt(i);
        }
        child.freq++;
    }

    public boolean search(String word) {
        TrieNode ptr = root;

        for(int i=0; i<word.length(); i++) {
            ptr = ptr.child_nodes[word.charAt(i) - 'a'];
            if(ptr == null) return false;
        }
        if(ptr.freq == 0) return false;

        return true;
    }

    public boolean startsWith(String prefix) {
        TrieNode ptr = root;

        for(int i=0; i<prefix.length(); i++) {
            ptr = ptr.child_nodes[prefix.charAt(i) - 'a'];
            if(ptr == null) return false;
        }

        return true;
    }
}
{% endhighlight %}
同时在TrieNode中可以加入freq这个变量，起到了之前加入$的作用，每次insert，叶子节点的freq会加一

这样的实现，search和startWith的代码就几乎相差不多，区别仅仅在于，search在匹配到最后一个字母时，需要检查一下这个节点是否是一个叶节点（freq是否不为0）

Trie的使用情景主要在于搜索引擎和文本处理，举例来说，要在一个10000词的文章中找到was这个词的出现次数，有如下几种策
假设单词的平均长度为d，有n个字符

1. 穷举法，将这些词全部读入数组中，然后再一一匹配，匹配次数O(n),字符串两两比较时间为O(d),时间复杂度为O(nd)

2. 平衡二叉搜索树，因为平衡，平均单词匹配次数为O(logn),时间复杂度为O(dlogn)

3. HashMap(哈希表),存储单词和出现次数，计算一个单词的哈希值所用时间为O(d),假设有m个桶，单词均匀分布在其中，字串的两两比较次数为O(n/m)，时间复杂度为O(n/m * d)

	补充一点知识，哈希表的两种实现方法，拉链法和线性探测法，拉链法就是每个桶（指针）指向一个链表，链表中的每个节点存储Key散列值为该桶索引的键值对
	线性探测法是遇到碰撞（散列值一样）就自动线性寻找下一个桶，如果桶为空则将Key-Value存入，否则继续向下一个桶存入

字典树(Trie)则有着更好的查找效率，使用别人的那种方法实现的Trie，因为每个节点直接存储了以该节点为叶节点的单词出现次数，所以解决如上问题，时间复杂度仅仅需要O(d) = O(1)，同时由于相同前缀单词共享空间的特性，内存使用也会更少一些

实际一点的应用，比如给一堆英文单词然后字典序排序，快排的速度最多为O(nlogn),但是使用字典树，直接将字典树插入完成后，中序遍历即可
（最近脑子老有浆糊，连二叉树的几个遍历都经常忘，二叉树先序遍历，根左右，中序遍历左根右，后序遍历左右根）