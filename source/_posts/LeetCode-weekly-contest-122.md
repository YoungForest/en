---
title: LeetCode weekly contest 122
date: 2019-02-04 18:38:09
tags:
- Competitive Programming
categories:
- LeetCode
---

Because I was staying at home for the holiday, I actually forgot what day of the week it was and only knew which day of the twelfth lunar month it was. Today I finally realized it was already Monday and that I had missed the weekly contest. On this Chinese New Year's Eve, before watching the Spring Festival Gala with my family, Forest and his whole family wish everyone a happy New Year! I will quickly finish these four contest problems so I can eat New Year's Eve dinner with peace of mind.

Since official Notes cannot be used during the contest, writing on the blog is a convenient substitute.

## 985. Sum of Even Numbers After Queries

The first problem can be solved by direct simulation. Each query has two actions:
1. add `val` to `A[index]`;
2. sum the even values of `A`.

The simulation time complexity is:
O(K * N)
where N is the array length and K is the query length.

Since both K and N are no greater than 10000, the total scale is around 10^8 < 10^9, which can AC.

```cpp
class Solution {
public:
    vector<int> sumEvenAfterQueries(vector<int>& A, vector<vector<int>>& queries) {
        vector<int> results;
        for (const vector<int>& query : queries) {
            A[query[1]] += query[0];
            int total = 0;
            for (int a : A) {
                if (a % 2 == 0) total += a;
            }
            results.push_back(total);
        }
        
        return results;
    }
};
```

However, the [Solution](https://leetcode.com/problems/sum-of-even-numbers-after-queries/solution/) gives an O(N+K) approach.
The idea is: each time we compute the sum, we can use the result from the previous query.
After adding:
if a value in `A` changes from odd to even, `sum = last_sum + new_even`;
if even -> odd, `sum = last_sum - old_even`;
if even -> even, `sum = last_sum + new_even - old_even`;
if odd -> odd, `sum = last_sum`.

```cpp
class Solution {
public:
    vector<int> sumEvenAfterQueries(vector<int>& A, vector<vector<int>>& queries) {
        vector<int> results;
        int total = 0;
        for (int a : A) {
            if (a % 2 == 0) total += a;
        }
        
        for (const vector<int>& query : queries) {
            if (A[query[1]] % 2 == 0) total -= A[query[1]];
            A[query[1]] += query[0];
            if (A[query[1]] % 2 == 0) total += A[query[1]];
            results.push_back(total);
        }
        
        return results;
    }
};
```

## 988. Smallest String Starting From Leaf

Find the smallest string from a leaf to the root in a binary tree.
Intuition: recursively solve the left and right subtrees, then combine the root node with the smaller result.

```cpp
/**
 * Definition for a binary tree node.
 * struct TreeNode {
 *     int val;
 *     TreeNode *left;
 *     TreeNode *right;
 *     TreeNode(int x) : val(x), left(NULL), right(NULL) {}
 * };
 */
class Solution {
public:
    string smallestFromLeaf(TreeNode* root) {
        if (!root) return "";
        string left = smallestFromLeaf(root->left);
        string right = smallestFromLeaf(root->right);
        if (!left.empty() && !right.empty()) {
            if (left < right) {
                left.push_back('a' + root->val);
                return left;
            } else {
                right.push_back('a' + root->val);
                return right;
            }
        } else if (!left.empty()) {
            left.push_back('a' + root->val);
            return left;
        } else {
            right.push_back('a' + root->val);
            return right;
        }
            
    }
};
```

## 986. Interval List Intersections

Find the intersection list of two sorted interval lists.

Intuition: use two pointers to traverse the two lists. Based on the degree of overlap, choose the intersection and move the pointers.

```cpp
/**
 * Definition for an interval.
 * struct Interval {
 *     int start;
 *     int end;
 *     Interval() : start(0), end(0) {}
 *     Interval(int s, int e) : start(s), end(e) {}
 * };
 */
class Solution {
public:
    vector<Interval> intervalIntersection(vector<Interval>& A, vector<Interval>& B) {
        vector<Interval> results;
        
        auto a = A.begin();
        auto b = B.begin();
        
        while (a != A.end() && b != B.end()) {
            if (a->end < b->start) {
                a++;
            } else if (b->end < a->start) {
                b++;
            } else if (a->start < b->start) {
                if (a->end < b->end) {
                    Interval interval(b->start, a->end);
                    results.push_back(interval);
                    a++;
                } else {
                    results.push_back(*b);
                    b++;
                }
            } else if (b->start < a->start) {
                if (b->end < a->end) {
                    Interval interval(a->start, b->end);
                    results.push_back(interval);
                    b++;
                } else {
                    results.push_back(*a);
                    a++;
                }
            } else {
                if (a->end <= b->end) {
                    Interval interval(b->start, a->end);
                    results.push_back(interval);
                    a++;
                } else {
                    Interval interval(a->start, b->end);
                    results.push_back(interval);
                    b++;
                }
                // cout << "(" << a->start << ", " << a->end << "), ";
                // cout << "(" << b->start << ", " << b->end << ")" << endl;
            }
        }
        
        return results;
    }
};
```

## 987. Vertical Order Traversal of a Binary Tree

Intuition: build a `deque<treemap<int>>`. For each subtree, insert `root` into the current treemap, using treemap to maintain order within the same X layer, while recursively calling the left and right subtrees and updating the current treemap.
Finally convert it to `vector<vector<int>>`.

```cpp
/**
 * Definition for a binary tree node.
 * struct TreeNode {
 *     int val;
 *     TreeNode *left;
 *     TreeNode *right;
 *     TreeNode(int x) : val(x), left(NULL), right(NULL) {}
 * };
 */
class Solution {
    deque<multiset<pair<int, int>>> results;
    void recurse(TreeNode* root, deque<multiset<pair<int, int>>>::iterator current, int level) {
        if (!root) return;
        if (current == results.end()) {
            multiset<pair<int, int>> s;
            results.push_back(s);
            current = results.end() - 1;
        }
        if (current + 1 == results.begin()) {
            multiset<pair<int, int>> s;
            results.push_front(s);
            current = results.begin();
        }
        current->insert({level, root->val});
        recurse(root->left, current - 1, level + 1);
        recurse(root->right, current + 1, level + 1);
    }
public:
    vector<vector<int>> verticalTraversal(TreeNode* root) {
        recurse(root, results.begin(), 0);
        
        vector<vector<int>> ret;
        for (const multiset<pair<int, int>>& s : results) {
            vector<int> v;
            for (const pair<int, int> num : s) {
                v.push_back(num.second);
            }
            ret.push_back(v);
        }
        
        return ret;
    }
};
```

## Postscript

The result of Google's phone interview has come out. Unfortunately, Forest failed and did not enter the next round. Although it was expected, it still felt quite sad. After comparing myself with peers, I found that I still have not solved enough LeetCode problems. At the beginning of the year, I set the goal at only 300 problems. Looking at how intense competition among programmers is now, 300 is probably far from enough. After all, I have already practiced around 200 problems and still could not handle an intern interview. After this failure, I re-examined my ability and decided to raise this year's problem-solving plan to 900 problems, or to finish all solvable problems. Since some problems require Premium, I do not yet know how many are available. After finishing the ones I should solve, I will also buy a premium membership and continue grinding.

I told my Kuaishou senior that C++ is my main language, and then he asked me what the C++11 feature "move semantics" is. Then I was done. My grasp of C++ is only superficial, yet I still dared to say I know C++. I must finish reading *C++ Primer* during the Spring Festival holiday; otherwise, after the holiday I will be too embarrassed to talk to my Kuaishou senior.

The road is long and difficult. Keep going, Forest!
