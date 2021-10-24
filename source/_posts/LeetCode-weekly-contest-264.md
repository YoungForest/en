---
title: LeetCode weekly contest 264
date: 2021-10-24 17:20:15
tags:
- Competitive Programming
categories:
- LeetCode
---

| Rank |	Name |	Score |	Finish Time | 	Q1 (3) |	Q2 (4) |	Q3 (5) |	Q4 (6)|
|--|--|--|--|--|--|--|--|
| 323 / 12700 | YoungForest | 	18 | 1:00:46 |  0:11:32 🐞1 | 0:27:23 | 0:42:54 🐞1 | 0:50:46 |

After 6 weeks of cruel daily check in, I am finally free from solve the daily problem everyday in cruel group. The condition of free is that the contest rank is within top 500 in the world and solving all 4 problems in weekly contest. Congratulations on me.

## 2047. Number of Valid Words in a Sentence

Sign on problem.
It is more difficult than usual. The corner case needs to be careful of. For example, '-' is not a valid word.


```python
class Solution:
    def countValidWords(self, sentence: str) -> int:
        def isPunctuation(c):
            return c == '!' or c == '.' or c == ','
        def isLower(c):
            return ord(c) >= ord('a') and ord(c) <= ord('z')
        def check(word):
            # It only contains lowercase letters, hyphens, and/or punctuation (no digits).
            # There is at most one hyphen '-'. If present, it should be surrounded by lowercase characters ("a-b" is valid, but "-ab" and "ab-" are not valid).
            # There is at most one punctuation mark. If present, it should be at the end of the token.
            hyphenCount = 0
            punctuationCount = 0
            for c in word:
                if c == '-':
                    hyphenCount += 1
                if c.isdigit():
                    return False
                if isPunctuation(c):
                    punctuationCount += 1
            if hyphenCount > 1 or punctuationCount > 1: return False
            if punctuationCount == 1 and not isPunctuation(word[-1]): return False
            if hyphenCount == 1:
                l = word.split('-')
                if len(l) != 2: return False
                if not (len(l[0]) >= 1 and isLower(l[0][-1])) or not (len(l[1]) >= 1 and isLower(l[1][0])): return False
            return True
        ans = 0
        words = sentence.split()
        for word in words:
            # print (word)
            if check(word):
                # print('Yes')
                ans += 1
        return ans
```

Time complexity: O(N),
Space complexity: O(N).

A easier solution is `re`, aka regular expression.

```python
import re

class Solution:
    def countValidWords(self, sentence: str) -> int:
        pattern = re.compile('(^[a-z]+(-[a-z]+)?)?[,.!]?$')
        word_count = 0
        for word in sentence.split():
            if pattern.match(word):
                word_count = word_count + 1
                
        return word_count
```

Time complexity: O(N),
Space complexity: O(N).

## 2048. Next Greater Numerically Balanced Number

Many people's solutions are "Accepted" using brute force. Iterate every number greater than n, and check if it is numerically balanced.
I proposed a better solution in contest. Because of `0 <= n <= 10^6`, I manually enumerate all numerically balanced numbers and find the next greater one. The possible balance numbers is countable. One trick here is using `permutations` to iterate all permutations of a string.

```python
class Solution:
    def nextBeautifulNumber(self, n: int) -> int:
        # 1digit: 1
        # 2digit: 22
        # 3digit: 333 or 1+2
        # 4digit: 1+3 or 4
        # 5digit: 5 or 1+4 or 2+3
        # 6digit: 6 or 1+5 or 2+4 or 1+2+3
        # 7digit: 7 or 1+6 or 1+2+4 1224444
        if n == 10**6: return 1224444
        ans = 1224444
        s = str(n)
        # 6! 720
        def check(l):
            ans = float('inf')
            origin = ''
            for i in l:
                origin += str(i) * i
            perms = [''.join(p) for p in permutations(origin)]
            for s in perms:
                i = int(s)
                if i > n:
                    ans = min(ans, i)
            return ans
            
        ans = min(ans, check([6]))
        ans = min(ans, check([1, 5]))
        ans = min(ans, check([2, 4]))
        ans = min(ans, check([1, 2, 3]))
        ans = min(ans, check([5]))
        ans = min(ans, check([1, 4]))
        ans = min(ans, check([2, 3]))
        ans = min(ans, check([4]))
        ans = min(ans, check([1, 3]))
        ans = min(ans, check([3]))
        ans = min(ans, check([1, 2]))
        ans = min(ans, check([2]))
        ans = min(ans, check([1]))
        return ans
```
Time complexity: O(13 * 6!),
Space complexity: O(6!).

## 2049. Count Nodes With the Highest Score

1. We could use `DFS` to calculate every subtrees' size. 
2. Calculate the score of each node. 3 possible states when deleting a node: two children, one child, no child. A corner case is the root node. It do not have the parent.
3. Find the highest score and its frequency.

A corner case is that the products could overflow `int`. It is `10^5 * 10^5` maximally. `long long` could avoid this runtime error easily.

```cpp
template <typename T>
ostream& operator <<(ostream& out, const vector<T>& a) {
  out << "["; bool first = true;
  for (auto& v : a) { out << (first ? "" : ", "); out << v; first = 0;} out << "]";
  return out;
}
using ll = long long;
class Solution {
public:
    int countHighestScoreNodes(vector<int>& parents) {
        const int n = parents.size();
        vector<vector<int>> children(n);
        for (int i = 1; i < n; ++i) {
            const int parent = parents[i];
            children[parent].push_back(i);
        }
        const int root = 0;
        vector<vector<ll>> subtreeSize(n);
        function<ll(const int)> dfs = [&](const int root) -> ll {
            ll ans = 1;
            for (int child : children[root]) {
                const ll childSize = dfs(child);
                subtreeSize[root].push_back(childSize);
                ans += childSize;
            }
            return ans;
        };
        const ll totalSize = dfs(0);
        vector<ll> scores(n);
        for (int i = 0; i < n; ++i) {
            if (children[i].size() == 0) {
                scores[i] = totalSize - 1;
            } else if (children[i].size() == 1) {
                if (totalSize - 1 == subtreeSize[i][0]) {
                    scores[i] = subtreeSize[i][0];
                } else {
                    scores[i] = subtreeSize[i][0] * (totalSize - 1 - subtreeSize[i][0]);
                }
            } else {
                if (totalSize - 1 - subtreeSize[i][0] - subtreeSize[i][1] == 0) {
                    scores[i] = subtreeSize[i][0] * subtreeSize[i][1];
                } else {
                    scores[i] = subtreeSize[i][0] * subtreeSize[i][1] * (totalSize - 1 - subtreeSize[i][0] - subtreeSize[i][1]);
                }
            }
        }
        // cout << scores << endl;
        const ll highestScore = *max_element(scores.begin(), scores.end());
        return count(scores.begin(), scores.end(), highestScore);
    }
};
```

Time complexity: O(n),
Space complexity: O(n).


## 2050. Parallel Courses III

Typical topo sort.
With `priority_queue`, we could iterate every course end events from time increasing.

```cpp
class Solution {
public:
    int minimumTime(int n, vector<vector<int>>& relations, vector<int>& time) {
        vector<int> indegree(n, 0);
        vector<vector<int>> out(n);
        for (const auto& r : relations) {
            const int p = r[0] - 1;
            const int n = r[1] - 1;
            indegree[n]++;
            out[p].push_back(n);
        }
        using pii = pair<int, int>; // end time, course number
        priority_queue<pii, vector<pii>, greater<>> events;
        for (int i = 0; i < n; ++i) {
            if (indegree[i] == 0) {
                events.push({time[i], i});
            }
        }
        int ans = 0;
        while (!events.empty()) {
            auto [currentTime, courseNumber] = events.top();
            ans = currentTime;
            events.pop();
            for (int to : out[courseNumber]) {
                indegree[to]--;
                if (indegree[to] == 0) {
                    events.push({currentTime + time[to], to});
                }
            }
        }
        return ans;
    }
};
```

Time complexity: O(N * logN),
Space complexity: O(N).