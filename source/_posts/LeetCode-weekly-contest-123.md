---
title: LeetCode weekly contest 123
date: 2019-02-10 10:29:44
tags:
- Competitive Programming
categories:
- LeetCode
translations:
  zh-CN: https://youngforest.github.io/2019/02/10/LeetCode-weekly-contest-123/
  en: https://youngforest.github.io/en/2019/02/10/LeetCode-weekly-contest-123/
---
This contest was the first one after the Spring Festival.

## 989. Add to Array-Form of Integer

Idea: simulate the written addition process and add digit by digit. The official Solution has a very vivid name for it: Schoolbook Addition.
Time complexity: O(max(N, M)), where N and M are the lengths of `A` and `K`, respectively.
Space complexity: O(M-N), namely the space used by the `deque`.

```cpp
class Solution {
public:
    vector<int> addToArrayForm(vector<int>& A, int K) {
        int index = A.size() - 1;
        while (K > 0 && index >= 0) {
            int last_digit = K % 10;
            K /= 10;
            A[index] += last_digit;
            int carry = A[index] / 10;
            K += carry;
            A[index] %= 10;
            index--;
        }
        
        deque<int> result;
        while (K > 0) {
            result.push_front(K % 10);
            K /= 10;
        }
        
        A.insert(A.begin(), result.begin(), result.end());
        
        return A;
    }
};
```

## 990. Satisfiability of Equality Equations

Idea: use Union-Find to store equality relationships, then iterate over all inequality relationships and check whether they are between different sets.

Time complexity: O(N),
space complexity: O(N).

```cpp
class Solution {
    array<char, 26> parents;
    void union_variables(char a, char b) {
        char a_parent = find_root(a);
        char b_parent = find_root(b);
        parents[a_parent - 'a'] = b_parent;
    }
    char find_root(char a) {
        while (parents[a - 'a'] != a)
            a = parents[a - 'a'];
        return a;
    }
public:
    Solution() {
        for (int i = 0; i < 26; i++) {
            parents[i] = 'a' + i;
        }
    }
    bool equationsPossible(vector<string>& equations) {
        for(const string & s : equations) {
            if (s[1] == '=') {
                union_variables(s[0], s[3]);
            }
        }
        
        for(const string & s : equations) {
            if (s[1] == '!') {
                if (find_root(s[0]) == find_root(s[3]))
                    return false;
            }
        }
        
        return true;
    }
};
```

## 991. Broken Calculator

Idea: once `X` is greater than `Y`, we can only reach `Y` by decrementing.
If `X` is smaller than `Y`, we can either `DOUBLE` or `Decrement`.
How should we choose then? We can observe that `Double, Decrement, Decrement` and `Decrement, Double` produce the same result.
From this, we can see:
If `Y` is odd, it must be produced by `Double, Decrement`.
If `Y` is even, it must be produced by `Double` (using `Decrement` takes more operations).

Time complexity: O(log Y);
space complexity: O(log Y), because of recursion.

```cpp
class Solution {
public:
    int brokenCalc(int X, int Y) {
        if (X == Y) return 0;
        if (X > Y) return X - Y;
        if (Y % 2 == 1) return brokenCalc(X, Y + 1) + 1;
        else return brokenCalc(X, Y / 2) + 1;
    }
};
```

## 992. Subarrays with K Different Integers

I had 40 minutes left for the last problem. I aimed to solve this Hard problem.

Idea: based on the problem, the time complexity looks like O(n ^ 2), since we need to traverse all subarrays at least once.
If we use two loops to enumerate all subarrays, how can we quickly obtain the number of distinct numbers in each subarray?
Dynamic programming can be used to reduce complexity.
For each subarray's distinct numbers, we use `hashmap<int, int>`, where key represents a distinct number and value represents its frequency.
Let `f(i, j)` be the hashmap corresponding to the distinct numbers in subarray `[i, j]`.
Then:
`f(i, j) =`
`f(i, j-1).insert(A[j])`
or
`f(i+1, j).delete(A[i])`
`new(A[i]), if i == j`.
If we build the hashmap bottom-up, the outer loop is `new` and the inner loop is `delete`.
The result was TLE.

Think about whether there is an O(n log n) solution.

```cpp
class Solution {
public:
    int subarraysWithKDistinct(vector<int>& A, int K) {
        unordered_set<int> frequency; // 如果没有delete的操作，使用unordered_set也是可以的
        int result = 0;
        for (int i = 0; i < A.size(); i++) {
            for (int j = i; j < A.size(); j++) {
                if (i == j) {
                    frequency.clear();
                }
                frequency.insert(A[j]);
                if (K == frequency.size()) result++;
                if (K < frequency.size()) break;
            }
        }
        
        return  result;
    }
};
```

In the end, I still did not come up with a further optimized algorithm. After reading the [Solution](https://leetcode.com/problems/subarrays-with-k-different-integers/solution/), I was surprised to find that there is an O(N) solution. My observation of the properties of the number of distinct values in subarrays was still not careful enough.

First, if we fix the right boundary, then for two subarrays `(i1, j)` and `(i2, j)`, where `i1 < i2`, we have `f(i1, j) <= f(i2, j)`.
Therefore, for each right boundary `j`, the left boundary of qualifying subarrays lies within a certain range, which can be written as `[left1, left2]`.

Second, as we grow the right boundary, if the number of distinct values exceeds `K`, we need to grow `left1` and `left2`. In other words, `left1` and `left2` increase monotonically with the right boundary.

```cpp
class Solution {
    struct Window {
        unordered_map<int, int> hashmap;
        int size = 0;
        void add(int value) {
            hashmap[value]++;
            if (hashmap[value] == 1) {
                size++;
            }
        } 
        void remove(int value) {
            hashmap[value]--;
            if (hashmap[value] == 0) {
                size--;
            }
        }
    };
public:
    int subarraysWithKDistinct(vector<int>& A, int K) {
        Window w1, w2;
        int left1 = 0, left2 = 0, right = 0;
        int result = 0;
        while (right < A.size()) {
            int value = A[right];
            
            w1.add(value);
            w2.add(value);
            
            while (w1.size > K) {
                w1.remove(A[left1++]);
            }
            while (w2.size >= K) {
                w2.remove(A[left2++]);
            }
            
            
            result += left2 - left1;
            right++;
        }
        
        return result;
    }
};
```

## Postscript

Today is one of my last three days at home. As expected, going home for winter vacation mostly wastes time. Staying at school in Beijing, together with like-minded classmates, gives me stronger motivation to study. The pressure of competition is also greater. No wonder everyone longs for a better environment and better friends.
<!-- Next semester, I will start interning at SenseTime. Make good use of this opportunity. -->
Keep going, Forest!
