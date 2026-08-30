---
title: LeetCode weekly contest 117
date: 2018-12-30 10:47:49
description: An experiment in blogging during the contest, covering univalued trees, equal adjacent differences, spell filters and observation lamps.
tags:
- Competitive Programming
categories:
- LeetCode
translations:
  zh-CN: https://youngforest.github.io/2018/12/30/LeetCode-weekly-contest-117/
  en: https://youngforest.github.io/en/2018/12/30/LeetCode-weekly-contest-117/
---
<figure class="editorial-illustration editorial-illustration--hero">
  <img src="/en/images/ai/LeetCode-weekly-contest-117/en-hero.webp" alt="Forest records while training with a single-color tree, equal-gap beads, layered sound filters and observation lamps on branches" width="1536" height="864" decoding="async" fetchpriority="high">
</figure>

<!-- more -->

Today I participated in LeetCode weekly contest 117 and used a different strategy: writing the blog summary while solving the problems. I hoped this would record my real thoughts more faithfully and improve my blogging efficiency. The previous two times, writing the blog afterward always delayed things by several days.

## 965. Univalued Binary Tree

A very simple, almost silly problem. Direct DFS/BFS is enough. Since BFS can return immediately when it encounters an invalid node, it is more convenient. I chose a BFS implementation.

```python
# Definition for a binary tree node.
# class TreeNode:
#     def __init__(self, x):
#         self.val = x
#         self.left = None
#         self.right = None

class Solution:
    def isUnivalTree(self, root):
        """
        :type root: TreeNode
        :rtype: bool
        """
        if root == None: return True
        univalue = root.val
        queue = [root]
        
        while len(queue) > 0:
            node = queue.pop(0)
            if node.left != None: queue.append(node.left)
            if node.right != None: queue.append(node.right)
            if node.val != univalue: return False
            
        return True
```

## 967. Numbers With Same Consecutive Differences

The second problem was also very straightforward. Direct search is enough. The special cases to pay attention to are `N == 1` and `K == 0`.

```python
from functools import reduce

class Solution:
    def numsSameConsecDiff(self, N, K):
        """
        :type N: int
        :type K: int
        :rtype: List[int]
        """
        if N == 1:
            return [0, 1, 2, 3, 4, 5, 6, 7, 8, 9]
        ret = []
        hashmap = {}
        for i in range (10):
            hashmap[i] = []
            if K == 0:
                hashmap[i].append(i)
            else:
                if i + K <= 9:
                    hashmap[i].append(i + K)
                if i - K >= 0:
                    hashmap[i].append(i - K)
        
        
        def helper(n, digits):
            if n == N:
                ret.append(reduce(lambda x, y: x * 10 + y, digits))
                return
            for i in hashmap[digits[n-1]]:
                digits.append(i)
                helper(n+1, digits)
                digits.pop()
                
        for i in range(1, 10):
            digits = [i]
            helper(1, digits)
            
        return ret
```

## 966. Vowel Spellchecker

Since I solved the first two problems with brute-force search, I also jumped straight into this one. The result was TLE, because the brute-force time complexity is O(MN), where M = len(wordlist) and N = len(queries).

How can we further reduce the time complexity? The first word that flashed through my mind was Trie, which I had just learned recently from *Algorithms, 4th Edition*. The time complexity would be O(max(N * length of the longest word in wordlist, M)). If nothing unexpected happened, it should be AC. But it was already 11:46, and I probably could not finish it in time. Let me look at the fourth problem first.

```python
class Solution:
    def spellchecker(self, wordlist, queries):
        """
        :type wordlist: List[str]
        :type queries: List[str]
        :rtype: List[str]
        """
        wordlist_replace_vowel = []
        vowel = ['a', 'e', 'i', 'o', 'u', 'A', 'E', 'I', 'O', 'U']
        word_lower = []
        for word in wordlist:
            word_lower.append(word.lower())
            temp = list(word)
            for i in range(len(temp)):
                if temp[i] in vowel:
                    temp[i] = '0'
            wordlist_replace_vowel.append(''.join(temp).lower())
            
        ret = []
        for query in queries:
            flag = 0
            match = ''
            temp = list(query)
            for i in range(len(temp)):
                if temp[i] in vowel:
                    temp[i] = '0'
            query_replace_vowel = ''.join(temp).lower()
            query_lower = query.lower()
            
            for wi, word in enumerate(wordlist):
                if flag < 4 and word == query:
                    match = word
                    flag = 4
                    break
                if flag  < 3 and word_lower[wi] == query_lower:
                    match = word
                    flag = 3
                if flag < 2 and wordlist_replace_vowel[wi] == query_replace_vowel:
                    match = word
                    flag = 2
            
            ret.append(match)
            
        return ret
```

After reading the Solution, I realized this was also a very straightforward problem. Brute force would TLE, but using a HashMap is enough.

Time complexity: O(C), where C is the total length of `wordlist` and `queries`.
Space complexity: O(C).

```python
class Solution:
    def spellchecker(self, wordlist, queries):
        """
        :type wordlist: List[str]
        :type queries: List[str]
        :rtype: List[str]
        """
        wordlist_replace_vowel = {}
        vowel = ['a', 'e', 'i', 'o', 'u']
        word_lower = {}
        word_set = set(wordlist)
        for word in wordlist:
            wl = word.lower()
            # setdefault 的使用：第一次赋值，之后忽略。保证在wordlist中先出现的词优先
            word_lower.setdefault(word.lower(), word)
            wordlist_replace_vowel.setdefault(''.join('0' if i in vowel else i for i in wl), word)
            
        ret = []
        for query in queries:
            ql = query.lower()
            query_replace_vowel = ''.join('0' if i in vowel else i for i in ql)
            if query in word_set:
                ret.append(query)
            elif ql in word_lower:
                ret.append(word_lower[ql])
            elif query_replace_vowel in wordlist_replace_vowel:
                ret.append(wordlist_replace_vowel[query_replace_vowel])
            else:
                ret.append('')
            
        return ret
```

## 968. Binary Tree Cameras

After reading the Solution, I was amazed: DP can be used like this!

Each node has three possible states:

1. The subtree is covered, but this node is not covered.
2. The subtree and this node are both covered, but this node does not have a camera.
3. The subtree and this node are both covered, and this node has a camera.

For a node in each state, the states of its children are also determined:

1. Both left and right children are in state 2.
2. The left and right children are in state 2 or 3, and at least one of them is in state 3.
3. The left and right children can be in state 1, 2, or 3, but we need to add one camera.

```python
# Definition for a binary tree node.
# class TreeNode:
#     def __init__(self, x):
#         self.val = x
#         self.left = None
#         self.right = None

class Solution:
    def minCameraCover(self, root):
        """
        :type root: TreeNode
        :rtype: int
        """
        def solve(root):
            if root is None: return 0, 0, math.inf
            
            L = solve(root.left)
            R = solve(root.right)
            
            dp0 = L[1] + R[1]
            dp1 = min(L[2] + min(R[1:]), R[2] + min(L[1:]))
            dp2 = 1 + min(L) + min(R)
            
            return dp0, dp1, dp2
        
        return min(solve(root)[1:])
```

## Summary

I joined the contest in the morning and finished writing the blog before 5 p.m.

Overall, the quality of the contest problems was not as high as before. I used to enjoy solving problems sorted by Liked, and the experience was very good.

The advantage of contests is the time limit. It forces you to solve problems within a limited amount of time. Even if you cannot figure out the solution, you temporarily cannot look at the editorial, which is otherwise a lazy way to practice.

These days are the New Year holiday. I plan to read part of *C++ Primer*, so that later I can write C++ on my resume.

After that, I will read CTCI to prepare for Google's first phone screen.

Then comes CSAPP, the computer systems book I like.
