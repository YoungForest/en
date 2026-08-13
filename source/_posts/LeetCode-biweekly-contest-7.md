---
title: LeetCode biweekly contest 7
date: 2019-08-25 23:46:29
tags:
- Competitive Programming
categories:
- LeetCode
translations:
  zh-CN: https://youngforest.github.io/2019/08/25/LeetCode-biweekly-contest-7/
  en: https://youngforest.github.io/en/2019/08/25/LeetCode-biweekly-contest-7/
---
| Rank |	Name |	Score |	Finish Time | 	Q1 (3) |	Q2 (4) |	Q3 (5) |	Q4 (6)|
|--|--|--|--|--|--|--|--|
| 106 / 1901 |	YoungForest | 18 | 1:01:13 | 0:05:46 | 0:27:13 | 0:35:34  | 1:01:13 |

This week I attended ByteDance's summer camp. The opening ceremony was on Saturday, so I completed this biweekly contest from the bed in a five-star hotel. It was wonderfully comfortable, and the contest result was still decent.
Since I had to attend the summer camp classes the next morning, I skipped the weekly contest. But I still joined Kick Start Round E in the afternoon, skipping the summer camp class for it. What can I say? I really want to go to Google.

The biweekly contest problems were not hard. They felt like the kind of beginner-level problems you see on other OJs.

## 1165. Single-Row Keyboard

This tests the use of a hash table. Just build the reverse mapping from letter to index.

Time complexity: O(N)
Space complexity: O(N)

```cpp
class Solution {
public:
    int calculateTime(string keyboard, string word) {
        vector<int> location(26);
        for (int i = 0; i < keyboard.size(); ++i) {
            location[keyboard[i] - 'a'] = i;
        }
        int ans = 0;
        int current = 0;
        for (char c : word) {
            ans += std::abs(location[c - 'a'] - current);
            current = location[c - 'a'];
        }
        return ans;
    }
};
```

## 1166. Design File System

This tests basic data structures (tree) and string processing.
Build a tree from the paths. Each file or directory is a node, and each node has a value. The lookup and insertion processes are similar to a Trie.
Time complexity: O(N).
Space complexity: O(N).

```cpp
class FileSystem {
    struct Node {
        unordered_map<string, shared_ptr<Node>> children;
        int value;
        Node(int v) : value(v) {}
    };
    vector<string> split(const string& path) {
        string tmp;
        vector<string> stk;
        stringstream ss(path);
        while(getline(ss,tmp,'/')) {
            stk.push_back(tmp);
        }
        return stk;
    }
    shared_ptr<Node> m;
public:
    FileSystem() {
        m = make_shared<Node>(0);
    }
    
    bool create(string path, int value) {
        auto words = split(path);
        auto current = m;
        for (int i = 1; i < words.size() - 1; ++i) {
            const auto& word = words[i];
            if (current->children.find(word) == current->children.end()) {
                return false;
            } else {
                current = current->children[word];
            }
        }
        if (current->children.find(words.back()) != current->children.end()) {
            return false;
        }
        current->children[words.back()] = make_shared<Node>(value);
        return true;
    }
    
    int get(string path) {
        auto words = split(path);
        auto current = m;
        for (int i = 1; i < words.size(); ++i) {
            const auto& word = words[i];
            if (current->children.find(word) == current->children.end()) {
                return -1;
            } else {
                current = current->children[word];
            }
        }
        return current->value;
    }
};

/**
 * Your FileSystem object will be instantiated and called as such:
 * FileSystem* obj = new FileSystem();
 * bool param_1 = obj->create(path,value);
 * int param_2 = obj->get(path);
 */
```

## 1167. Minimum Cost to Connect Sticks

Greedy algorithm. Each time, choose the two sticks with the smallest cost and connect them. The earlier a stick is connected, the more likely its length is to affect the total cost.

```cpp
class Solution {
public:
    int connectSticks(vector<int>& sticks) {
        /*N个sticks，需要N-1次connect
        4
            4,5,8 9
            8,9 17
            17
        */
        int ans = 0;
        priority_queue<int, vector<int>, greater<int>> pq;
        for (int s : sticks) {
            pq.push(s);
        }
        while (pq.size() > 1) {
            int smallest = pq.top();
            pq.pop();
            int second = pq.top();
            pq.pop();
            ans += smallest + second;
            pq.push(smallest + second);
        }
        return ans;
    }
};
```

## 1168. Optimize Water Distribution in a Village

A variant of Minimum Spanning Tree.
Pipes are edges. In addition, wells are also edges; they can be treated as edges connected to the water source.

Time complexity: O(E log E)
Space complexity: O(N)

```cpp
class Solution {
    struct UF {
        vector<int> parents;
        UF(int n) {
            parents.resize(n);
            for (int i = 0; i < n; ++i) {
                parents[i] = i;
            }
        }
        int find(int x) {
           return x == parents[x] ? x : parents[x] = find(x);
        }
        void unin(int x, int y) {
            int xp = find(x);
            int yp = find(y);
            parents[xp] = yp;
        }
    };
public:
    int minCostToSupplyWater(int n, vector<int>& wells, vector<vector<int>>& pipes) {
        // 最小生成树
        // 每次寻找最短的边
        vector<tuple<int, int, int>> edges;
        for (int i = 0; i < wells.size(); ++i) {
            edges.push_back(make_tuple(wells[i], 0, i + 1));
        }
        for (const auto& pipe : pipes) {
            edges.push_back(make_tuple(pipe[2], pipe[0], pipe[1]));
        }
        sort(edges.begin(), edges.end());
        UF uf(n + 1);
        int ans = 0;
        for (const auto& e : edges) {
            int cost = get<0>(e);
            int left = get<1>(e);
            int right = get<2>(e);
            if (uf.find(left) == uf.find(right))
                ;
            else {
                ans += cost;
                uf.unin(left, right);
            }
        }
        return ans;
    }
};
```
