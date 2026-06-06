---
title: LeetCode weekly contest 187
date: 2020-05-04 10:44:42
tags:
- Competitive Programming
categories:
- LeetCode
---

| Rank |	Name |	Score |	Finish Time | 	Q1 (3) |	Q2 (4) |	Q3 (5) |	Q4 (7)|
|--|--|--|--|--|--|--|--|
| 301 / 12353 |	YoungForest | 19 | 1:03:24 |  0:06:34 |  0:03:07 |  0:17:30 | 1:03:24 |

## 1436. Destination City

Traverse each edge and count the out-degree of each point.

Time complexity: O(path.length * city[i].length),
Space complexity: O(city.length * city[i].length).

```cpp
class Solution {
public:
    string destCity(vector<vector<string>>& paths) {
        unordered_map<string, int> outgoing;
        unordered_set<string> seen;
        for (const auto& v : paths) {
            ++outgoing[v[0]];
            seen.insert(v[0]);
            seen.insert(v[1]);
        }
        for (const auto& s : seen) {
            if (outgoing[s] == 0)
                return s;
        }
        return "";
    }
};
```

## 1437. Check If All 1's Are at Least Length K Places Away

One pass. Record the position where the previous 1 appeared.

Time complexity: O(N),
Space complexity: O(1).

```cpp
class Solution {
    const int INF = 0x3f3f3f3f;
public:
    bool kLengthApart(vector<int>& nums, int k) {
        int last_one = -INF;
        for (int i = 0; i < nums.size(); ++i) {
            if (nums[i] == 1) {
                if (i - last_one - 1 >= k) {
                    last_one = i;
                } else {
                    return false;
                }
            }
        }
        return true;
    }
};
```

## 1438. Longest Continuous Subarray With Absolute Diff Less Than or Equal to Limit

Two pointers. Use a `multiset` to maintain the elements in the subarray. Because of the properties of TreeMap-like structures, we can quickly locate the maximum and minimum values in the subarray.

Time complexity: O(N log N),
Space complexity: O(N).

```cpp
class Solution {
public:
    int longestSubarray(vector<int>& nums, int limit) {
        multiset<int> subarray;
        size_t ans = 0;
        for (int r = 0, l = 0; r < nums.size(); ++r) {
            while (!subarray.empty() && nums[r] - *subarray.begin() > limit) {
                auto it = subarray.find(nums[l++]);
                subarray.erase(it);
            }
            while (!subarray.empty() && *subarray.rbegin() - nums[r] > limit) {
                auto it = subarray.find(nums[l++]);
                subarray.erase(it);
            }
            subarray.insert(nums[r]);
            ans = max(ans, subarray.size());
        }
        return ans;
    }
};
```

## 1439. Find the Kth Smallest Sum of a Matrix With Sorted Rows

Use a priority queue to record all candidates. Select the combination with the smallest sum, and add the changed possible combinations into the priority queue.

One thing to note is that the same candidate may be generated from different states, such as `001, 010 -> 011`. Here I used a set and string encoding to deduplicate.

Time complexity: O(k * m * m * log (nm)),
Space complexity: O(n * m * m).

```cpp
class Solution {
    string encode(const vector<int>& v) {
        string ans;
        for (int i : v) {
            ans.push_back('a' + i);
        }
        return ans;
    }
public:
    int kthSmallest(vector<vector<int>>& mat, int k) {
        const int m = mat.size();
        const int n = mat[0].size();
        int rank = 0;
        priority_queue<pair<int, vector<int>>,std::vector<pair<int,vector<int>>>, std::greater<pair<int,vector<int>>>> pq; // difference, row_index
        unordered_set<string> seen;
        int current_sum = 0;
        for (int i = 0; i < m; ++i) {
            current_sum += mat[i][0];
        }
        {
            vector<int> index(m, 0);
            pq.push({current_sum, index});
            seen.insert(encode(index));
        }
        
        while (rank < k && !pq.empty()) {
            auto t = pq.top();
            pq.pop();
            current_sum = t.first;
            for (int i = 0; i < m; ++i) {
                ++t.second[i];
                string x = encode(t.second);
                if (t.second[i] < n && seen.find(x) == seen.end()) {
                    pq.push({current_sum + mat[i][t.second[i]] - mat[i][t.second[i]-1], t.second});
                    seen.insert(x);
                }
                --t.second[i];
            }
            ++rank;
        }
        return current_sum;
    }
};
```
