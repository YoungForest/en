---
title: 'LeetCode #538 Convert BST to Greater Tree'
date: 2018-09-14 19:15:07
tags:
categories:
- LeetCode
---

Description: https://leetcode.com/problems/convert-bst-to-greater-tree/description/
Solution: https://leetcode.com/problems/convert-bst-to-greater-tree/solution/
Difficulty: Easy

Although this problem is Easy, writing a correct solution in one pass is still difficult.
When you see a binary tree, you should think of solving it recursively. One trick in this problem is how to pass along the value that needs to be accumulated during recursion.

When recursively calling the right subtree, we need to return the sum of the entire right subtree and add that sum to the root node. In my specific solution, the right subtree's sum may exist in two places: 1. the leftmost leaf node of the right subtree; 2. the root node of the right subtree, when the right subtree has no left child.

When recursively calling the left subtree, we need to pass in information from the root node and the right subtree sum, adding it to the leftmost leaf of the right subtree, which is the largest value in that subtree.

```python
# Definition for a binary tree node.
# class TreeNode:
#     def __init__(self, x):
#         self.val = x
#         self.left = None
#         self.right = None

class Solution:
    def convertBST(self, root):
        """
        :type root: TreeNode
        :rtype: TreeNode
        """
        def convert(root, addition):
            if root == None:
                return addition
            root.val += convert(root.right, addition)
            if root.left == None:
                return root.val
            return convert(root.left, root.val)
        
        convert(root, 0)
        
        return root
```

Time complexity is O(n), because every node has to be traversed once.
Space complexity is O(log n) on average and O(n) in the worst case, determined by the maximum recursion depth.

After reading the official Solution, I realized that I had made the problem too complicated. The accumulated value can be maintained through a global variable. Then doing a reverse in-order traversal of the tree makes the code much simpler. Approach #3 in the Solution is best, and its space complexity is O(1).

## Iterative Version Without Recursion

```python
# Definition for a binary tree node.
# class TreeNode:
#     def __init__(self, x):
#         self.val = x
#         self.left = None
#         self.right = None

class Solution:
    def convertBST(self, root):
        """
        :type root: TreeNode
        :rtype: TreeNode
        """
        total = 0
        stack = []
        
        node = root
        
        while len(stack) > 0 or node != None:
            while node != None:
                stack.append(node)
                node = node.right
            
            node = stack.pop()
            
            total += node.val
            node.val = total
            
            node = node.left
        
        return root
```
