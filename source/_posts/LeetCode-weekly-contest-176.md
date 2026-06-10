---
title: LeetCode weekly contest 176
date: 2020-02-16 12:07:55
tags:
- Competitive Programming
categories:
- LeetCode
---


| Rank |	Name |	Score |	Finish Time | 	Q1 (3) |	Q2 (5) |	Q3 (5) |	Q4 (6)|
|--|--|--|--|--|--|--|--|
| 306 / 8105 |	YoungForest | 	19 | 	1:36:07 | 0:02:47 | 0:23:09 | 0:54:53  1 | 1:26:07  1 |

In this contest I finally got AC at the last moment, which was extremely thrilling. Since joining the students in the China region, it has been hard for my weekly contest rank to enter the top 200. For example, this time I dropped from 229 to 306. I have to admit that domestic competition is fierce.

The main reason for the lower ranking was that I spent a lot of time debugging and trying things on the third problem, and almost had no time left to implement the last one. The recent lack of practice has also caused my debug ability and one-pass bug-free ability to drop sharply.

## 1351. Count Negative Numbers in a Sorted Matrix

Using the fact that both rows and columns are sorted, we can count in O(m + n).
It is similar to [search-a-2d-matrix-ii](https://leetcode.com/problems/search-a-2d-matrix-ii/).

Time complexity: O(m + n),
space complexity: O(1).

```cpp
class Solution {
public:
    int countNegatives(vector<vector<int>>& grid) {
        int ans = 0;
        const int m = grid.size();
        const int n = grid[0].size();
        int row = 0, col = n - 1;
        while (row < m && col >= 0) {
            if (grid[row][col] < 0) {
                ans += m - row;
                --col;
            } else {
                ++row;
            }
        }
        return ans;
    }
};
```

## 1352. Product of the Last K Numbers

Record the position of the last zero and the cumulative product. Determine whether the last K numbers contain a zero. If not, divide the last cumulative product by the product before the k-th number from the end.

Time complexity:
- add: O(1)
- getProduct: O(1)
Space complexity: O(N)

```cpp
class ProductOfNumbers {
    vector<int> product;
    int zeros = 0;
    int index = 1;
public:
    ProductOfNumbers() {
        product.push_back(1);
    }
    
    void add(int num) {
        if (num == 0) {
            product.push_back(1);
            zeros = index;
        }
        else
            product.push_back(num * product.back());
        ++index;
    }
    
    int getProduct(int k) {
        int b = index - k;
        int e = index; // [b, e)
        if (zeros < b) {
            return product.back() / product[product.size() - k - 1];
        } else {
            return 0;
        }
    }
};

/**
 * Your ProductOfNumbers object will be instantiated and called as such:
 * ProductOfNumbers* obj = new ProductOfNumbers();
 * obj->add(num);
 * int param_2 = obj->getProduct(k);
 */
```

## 1353. Maximum Number of Events That Can Be Attended

Greedy strategy.
Always attend the event with the earliest ending time.

Time complexity: O(N log N),
space complexity: O(N).

```cpp
class Solution {
public:
    int maxEvents(vector<vector<int>>& events) {
        int ans = 0;
        int current_day = 1;
        multiset<int> q;
        int i = 0;
        sort(begin(events), end(events), [](const auto& a, const auto& b) -> bool {
            if (a[0] != b[0]) {
                return a[0] < b[0];
            } else {
                return a[1] < b[1];
            }
        });
        while (i < events.size() || !q.empty()) {
            while (i < events.size() && events[i][0] <= current_day) {
                q.insert(events[i][1]);
                ++i;
            }
            while (!q.empty() && *q.begin() < current_day) {
                q.erase(q.begin());
            }
            if (!q.empty() && *q.begin() >= current_day) {
                ++ans;
                // cout << *q.begin() << endl;
                q.erase(q.begin());
            }
            ++current_day;
        }
        return ans;
    }
};
```

## 1354. Construct Target Array With Multiple Sums

This problem looks hard, but once you discover that the result of each replace operation is always the largest number, you can reverse the process from the final array back to the initial array.

Time complexity: O(N log N max_number)
Space complexity: O(N).

```cpp
class Solution {
    using ll = long long;
public:
    bool isPossible(vector<int>& target) {
        multimap<ll, ll> m;
        ll s = 0;
        for (int i = 0; i < target.size(); ++i) {
            m.insert({target[i], i});
            s += target[i];
        }
        function<bool()> recurse = [&]() -> bool {
            if (s == target.size())
                return true;
            auto max_item = m.end();
            --max_item;
            ll left = max_item->first;
            ll index = max_item->second;
            ll others = s - left;
            ll x = left - others;
            // cout << left << " " << index << " " << others << " " << x << endl;
            if (x < 1)
                return false;
            s = left;
            m.erase(max_item);
            m.insert({x, index});
            target[index] = x;
            return recurse();
        };
        return recurse();
    }
};
```
