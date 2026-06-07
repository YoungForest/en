---
title: LeetCode Weekly Contest 214
date: 2020-11-08 18:20:06
tags:
- Competitive Programming
categories:
- LeetCode
---

| Rank |	Name |	Score |	Finish Time | 	Q1 (3) |	Q2 (4) |	Q3 (5) |	Q4 (6)|
|--|--|--|--|--|--|--|--|
| 393 / 9769 | YoungForest | 18 | 1:36:40 | 0:05:13 | 0:11:24 | 0:44:59 |  1:26:40  2 |

My ranking in the Cruel group has stayed at 14th. It seems this is where my level has converged. Recently, because I have been busy writing my master's thesis, and on the other hand autumn recruitment has ended, I have not even done check-in problems. Only in November did I resume doing the daily problem on the US and Chinese servers to earn points for clothes. As for the Cruel group, because I can basically exempt myself from check-in, I have not checked in there for a month.

## 5561. Get Maximum in Generated Array

Warm-up problem. Fill in each element according to the recurrence formula.

```cpp
class Solution {
public:
    int getMaximumGenerated(int n) {
        vector<int> nums(n + 1);
        int ans = 0;
        nums[0] = 0;
        if (nums.size() <= 1) return 0;
        nums[1] = 1;
        ans = 1;
        for (int i = 2; i <= n; ++i) {
            if (i % 2 == 0) {
                nums[i] = nums[i / 2];
            } else {
                nums[i] = nums[i / 2] + nums[i / 2 + 1];
            }
            ans = max(ans, nums[i]);
        }
        return ans;
    }
};
```

Time complexity: O(N),
space complexity: O(N).

## 5562. Minimum Deletions to Make Character Frequencies Unique

Greedy. Keep deleting characters until an empty frequency slot appears.

```cpp
class Solution {
public:
    int minDeletions(string s) {
        vector<int> cnt(26, 0);
        for (char c : s) {
            ++cnt[c - 'a'];
        }
        set<int> count;
        int ans = 0;
        for (int a : cnt) {
            if (a != 0) {
                while (a != 0 && count.find(a) != count.end()) {
                    ++ans;
                    --a;
                }
                count.insert(a);
            }
        }
        return ans;
    }
};
```

Time complexity: O(N),
space complexity: O(26).

## 5563. Sell Diminishing-Valued Colored Balls

Greedy. Each time, try to take from the color with the most balls first. During the taking process, you do not need to take one by one; you can take as many as possible at once.
Use a priority queue to maintain the maximum color count, and use an arithmetic progression sum to compute the cost of taking balls.

```cpp
class Solution {
    using ll = long long;
    const ll MOD = 1e9 + 7;
    using pii = pair<int, int>;
public:
    int maxProfit(vector<int>& inventory, int o) {
        ll orders = o;
        // greedy
        ll ans = 0;
        map<ll, ll, greater<ll>> cnt;
        for (ll i : inventory) {
            ++cnt[i];
        }
        cnt[0] = 0;
        while (orders > 0) {
            auto first = *cnt.begin();
            if (first.first == 0) return -1;
            auto second = *next(cnt.begin());
            cnt.erase(cnt.begin());
            cnt.erase(cnt.begin());
            ll balls = (first.first - second.first) * first.second;
            pii newSecond = {second.first, second.second + first.second};
            ll reduce = min(orders, balls);
            orders -= reduce;
            // cout << first.first << " " << first.second << " " << reduce << endl;
            const ll batch = reduce / first.second;
            const ll remain = reduce % first.second;
            ans = (ans + (first.first * batch - batch*(batch-1) / 2) * first.second) % MOD;
            ans = (ans + (first.first - batch) * remain) % MOD;
            // for (int i = 0; i * first.second < reduce; ++i) {
            //     // cout <<  min(first.second, reduce - i) << " " <<  (first.first - i) << endl;
            //     ans = (ans + min(first.second, reduce - i * first.second) * (first.first - i)) % MOD;
            // }
            // cout << ans << endl;
            cnt.insert(newSecond);
        }
        return ans;
    }
};
```

Time complexity: O(N log N), N = inventory.size()
space complexity: O(N).

The time complexity is not that obvious. You can consider the fact that the size of `cnt` decreases by one each time, so the `while` loop is executed at most N times.

## 5564. Create Sorted Array through Instructions

The core is that we need a data structure that can count the number of elements in a certain range. We can use a segment tree, Binary Indexed Tree (BIT), rank tree, and so on. During the contest, I first used a rank tree template implemented with GNU pbds, but it timed out. In theory, the complexity should have been fine.
Then, because [the previous day's daily problem on the Chinese server](https://leetcode-cn.com/problems/count-of-range-sum/solution/qu-jian-he-de-ge-shu-by-leetcode-solution/) also used the same data structure, I copied a treap template. Because I was unfamiliar with the interface, debugging alone took half an hour. Fortunately, I ACed in the last 5 minutes, so I did not need to check in with the Cruel group this week again. I had already skipped it for a month.
Note that in the template interface, `rank` starts from index 1.

```cpp
class BalancedTree {
private:
    struct BalancedNode {
        long long val;
        long long seed;
        int count;
        int size;
        BalancedNode* left;
        BalancedNode* right;

        BalancedNode(long long _val, long long _seed): val(_val), seed(_seed), count(1), size(1), left(nullptr), right(nullptr) {}

        BalancedNode* left_rotate() {
            int prev_size = size;
            int curr_size = (left ? left->size : 0) + (right->left ? right->left->size : 0) + count;
            BalancedNode* root = right;
            right = root->left;
            root->left = this;
            root->size = prev_size;
            size = curr_size;
            return root;
        }

        BalancedNode* right_rotate() {
            int prev_size = size;
            int curr_size = (right ? right->size : 0) + (left->right ? left->right->size : 0) + count;
            BalancedNode* root = left;
            left = root->right;
            root->right = this;
            root->size = prev_size;
            size = curr_size;
            return root;
        }
    };

private:
    BalancedNode* root;
    int size;
    mt19937 gen;
    uniform_int_distribution<long long> dis;

private:
    BalancedNode* insert(BalancedNode* node, long long x) {
        if (!node) {
            return new BalancedNode(x, dis(gen));
        }
        ++node->size;
        if (x < node->val) {
            node->left = insert(node->left, x);
            if (node->left->seed > node->seed) {
                node = node->right_rotate();
            }
        }
        else if (x > node->val) {
            node->right = insert(node->right, x);
            if (node->right->seed > node->seed) {
                node = node->left_rotate();
            }
        }
        else {
            ++node->count;
        }
        return node;
    }

public:
    BalancedTree(): root(nullptr), size(0), gen(random_device{}()), dis(LLONG_MIN, LLONG_MAX) {}

    long long get_size() const {
        return size;
    }

    void insert(long long x) {
        ++size;
        root = insert(root, x);
    }

    long long lower_bound(long long x) const {
        BalancedNode* node = root;
        long long ans = LLONG_MAX;
        while (node) {
            if (x == node->val) {
                return x;
            }
            if (x < node->val) {
                ans = node->val;
                node = node->left;
            }
            else {
                node = node->right;
            }
        }
        return ans;
    }

    long long upper_bound(long long x) const {
        BalancedNode* node = root;
        long long ans = LLONG_MAX;
        while (node) {
            if (x < node->val) {
                ans = node->val;
                node = node->left;
            }
            else {
                node = node->right;
            }
        }
        return ans;
    }

    pair<int, int> rank(long long x) const {
        BalancedNode* node = root;
        int ans = 0;
        while (node) {
            if (x < node->val) {
                node = node->left;
            }
            else {
                ans += (node->left ? node->left->size : 0) + node->count;
                if (x == node->val) {
                    return {ans - node->count + 1, ans};
                }
                node = node->right;
            }
        }
        return {INT_MIN, INT_MAX};
    }
};

class Solution {
    const int MOD = 1e9 + 7;
    using ll = long long;
public:
    int createSortedArray(vector<int>& instructions) {
        BalancedTree* treap = new BalancedTree();
        int ret = 0;
        for (long long i: instructions) {
            // numLeft is the smallest >= i
            long long numLeft = treap->lower_bound(i);
            // cout << "numLeft: " << numLeft << endl;
            // rankLeft is the rank of numLeft, starting from 0
            ll rankLeft = (numLeft == LLONG_MAX ? treap->get_size(): treap->rank(numLeft).first - 1);
            // numRight is the smallest > i
            long long numRight = treap->upper_bound(i);
            // cout << "numRight: " << numRight << endl;
            // rankLeft is the rank of numLeft, starting from 0
            ll rankRight = (numRight == LLONG_MAX ? treap->get_size(): treap->rank(numRight).first - 1);
            // cout << rankLeft - 1 << " " << treap->get_size() - rankRight << endl;
            ret = (ret + min(treap->get_size() - rankRight, rankLeft)) % MOD;
            treap->insert(i);
        }
        return ret;
    }
};
```

Time complexity: O(N log N),
space complexity: O(N).
