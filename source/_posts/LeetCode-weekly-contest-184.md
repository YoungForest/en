---
title: LeetCode Weekly Contest 184
date: 2020-04-12 20:04:46
tags:
- Competitive Programming
categories:
- LeetCode
---

| Rank |	Name |	Score |	Finish Time | 	Q1 (4) |	Q2 (4) |	Q3 (6) |	Q4 (7)|
|--|--|--|--|--|--|--|--|
| 589 / 9816 |	YoungForest | 19 | 0:55:30 | 0:07:04 |  0:15:05 | 0:37:18  1 | 0:50:30 |

Today was another speed contest. Python tells us, "Life is short; I use Python." I implemented two problems in Python, problems 1 and 4. In fact, for problem 3, directly calling Python's Str API would have been even smoother, but at the time I chose to implement it with Trie + automaton, so it was only fair that my ranking dropped.

## 1408. String Matching in an Array

A warm-up problem. Just use Python Str's `find` function to determine whether a word is a substring.

Time complexity: O(words.length ^ 2 * words[i].length),
space complexity: O(words.length * words[i].length).

```python
class Solution:
    def stringMatching(self, words: List[str]) -> List[str]:
        ans = set()
        n = len(words)
        for i in range(n):
            for j in range(i + 1, n):
                if words[i].find(words[j]) != -1:
                    ans.add(words[j])
                if words[j].find(words[i]) != -1:
                    ans.add(words[i])
        return [i for i in ans]
```

## 1409. Queries on a Permutation With Key

Use `List` to simulate the changes of the permutation.
Use sequential search when looking for a position.

Time complexity: O(m + queries.length * m),
space complexity: O(m + queries.length).

```cpp
class Solution {
public:
    vector<int> processQueries(vector<int>& queries, int m) {
        list<int> P;
        for (int i = 1; i <= m; ++i) {
            P.push_back(i);
        }
        vector<int> ans(queries.size());
        for (int i = 0; i < queries.size(); ++i) {
            auto it = P.begin();
            int position = 0;
            while (it != P.end() && *it != queries[i]) {
                ++it;
                ++position;
            }
            ans[i] = position;
            P.push_front(*it);
            P.erase(it);
        }
        return ans;
    }
};
```

The discussion section provides a [Fenwick Tree solution](https://leetcode.com/problems/queries-on-a-permutation-with-key/discuss/575019/Python-Fenwick-tree-O(n-log-n)), with time complexity O(queries.length * log m + m * log m).

## 1410. HTML Entity Parser

An automaton, using a Trie to maintain state transitions.

Time complexity: O(text.length),
space complexity: O(text.length + 1).

```cpp
class Solution {
    struct Trie {
        unordered_map<char, shared_ptr<Trie>> children;
        char replace;
        bool valid = false;
        void add(const string& s, char c, int index = 1) {
            if (index == s.size()) {
                valid = true;
                replace = c;
                return;
            }
            if (children[s[index]] == nullptr) {
                children[s[index]] = make_shared<Trie>();
            }
            children[s[index]]->add(s, c, index + 1);
        }
    };
public:
    string entityParser(string text) {
        shared_ptr<Trie> root = make_shared<Trie>();
        root->add("&quot;", '"');
        root->add("&apos;", '\'');
        root->add("&amp;", '&');
        root->add("&gt;", '>');
        root->add("&lt;", '<');
        root->add("&frasl;", '/');
        string ans;
        int  i = 0;
        while (i < text.size())  {
            if (text[i] == '&') {
                int j = i + 1;
                auto current = root;
                while (j < text.size() && current->children[text[j]] != nullptr) {
                    current = current->children[text[j++]];
                }
                if (current->valid) {
                    ans.push_back(current->replace);
                    i = j;
                } else {
                    ans.push_back(text[i++]);
                }
            } else {
                ans.push_back(text[i++]);
            }
        }
        return  ans;
    }
};
```

## 1411. Number of Ways to Paint N x 3 Grid

A recursive solution, determining the number of painting ways row by row. The key is to define the representation of whether the current row is valid and whether it is compatible with the previous row.

Time complexity: O(n),
space complexity: O(n) -> O(1). At most, only the states of two rows need to be stored.

```python
class Solution:
    def numOfWays(self, n: int) -> int:
        import functools
        @functools.lru_cache(None)
        def unzip(x: int) -> List[int]:
            a = x // (3*3)
            b = (x // (3)) % 3
            c = x % 3
            return [a, b, c]
        @functools.lru_cache(None)
        def row_not_valid(state: int) -> bool:
            a, b, c = unzip(state)
            return a == b or b == c
        @functools.lru_cache(None)
        def between_row_valid(a: int, b: int) -> bool:
            al = unzip(a)
            bl = unzip(b)
            return all(al[i] != bl[i] for i in range(3))
            
        @functools.lru_cache(None)
        def dp(n: int, state: int) -> int:
            if row_not_valid(state):
                return 0
            else:
                if n == 0:
                    return 1
                else:
                    ans = 0
                    for i in range(3**3):
                        if not row_not_valid(i) and between_row_valid(state, i):
                            ans += dp(n-1, i)
                    return ans
        
        ans = 0
        for i in range(3**3):
            if not row_not_valid(i):
                ans += dp(n-1, i)
        return ans % (1000000000 + 7)
```
