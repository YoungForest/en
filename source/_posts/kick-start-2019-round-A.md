---
title: Kick start 2019 round A
date: 2019-03-24 13:29:15
tags:
- Competitive Programming
categories:
- KickStart
description: "A first serious contest round explores selecting a matched training group, placing one new service office and fairly allocating overlapping seat requests."
translations:
  zh-CN: https://youngforest.github.io/2019/03/24/kick-start-2019-round-A/
  en: https://youngforest.github.io/en/2019/03/24/kick-start-2019-round-A/
---
[Problem link]

This was my first time participating in Kick Start. Back in undergrad, I had joined its predecessor, Code Jam, with my roommate tls. This year I finally started preparing seriously for the Kick Start series. The reason is that it is one of Google's channels for selecting software engineers, and Google is my dream company.
On May 22, I attended Google's campus talk at Tsinghua. At the talk, the seniors also emphasized the importance of preparing for and participating in Kick Start. As a very left-leaning company, Google especially emphasizes fairness. Kick Start is a tool for making recruiting fairer. Compared with other companies' excessive emphasis on referrals, Kick Start gives students from less advantaged schools an opportunity.

Because of a platform failure, submissions were unavailable for the last 25 minutes. I had already given up one hour early, but I received an email that evening explaining the bug. Without that bug, my rank might have fallen further.
My final rank was 600/3305.
My scores were:
| | Training  | Parcels | Contention | Total |
|--|--|--|--|--|
| Mine | 20 | 15 | 0 | 35 |
| Total | 20 | 35 | 45 | 100 |

That is, I passed the warm-up problem and the small case of the second problem.
Overall, the problems were much harder than LeetCode. In the end, only two people got a full score.

<figure class="editorial-illustration editorial-illustration--hero">
  <img src="/en/images/ai/kick-start-2019-round-A/en-hero.webp" alt="A folded planning board turns uneven training pieces, a distant city grid and overlapping seat ribbons into three increasingly fair arrangements" width="1536" height="864" decoding="async" fetchpriority="high">
</figure>

<!-- more -->

## 1. Training

From a team of `N` people, choose `P` people. The goal is to minimize the total training hours. The training hours equal the sum, over every chosen person, of `the maximum skill point among the chosen people - this person's skill point`.
Intuition:
Sort first. Then keep sliding a window of length `P` and compute the minimum value.

Time complexity: O(N log N), because of sorting.
Space complexity: O(N).

```cpp
#include <iostream>
#include <algorithm>
#include <vector>

using namespace std;

int solution(vector<int> &skills, int N, int P) {
    sort(skills.begin(), skills.end());

    int need_hour = 0;
    for (int i = 0; i < P - 1; i++) {
        need_hour += skills[P - 1] - skills[i];
    }
    int ret = need_hour;
    int left = 0, right = P - 1;
    while (right < N) {
        right++;
        need_hour += (P - 1) * (skills[right] - skills[right - 1]);
        need_hour -= skills[right - 1] - skills[left];
        left++;
        ret = min(ret, need_hour);
    }
    
    return ret;
}

int main() {
    int T;
    cin >> T;
    
    for (int i = 0; i < T; i++) {
        int N, P;
        cin >> N >> P;
        vector<int> skills;
        for (int j = 0; j < N; j++) {
            int s;
            cin >> s;
            skills.push_back(s);
        }
        cout << "Case #" << i + 1 << ": " << solution(skills, N, P) << endl;
    }
}
```

## 2. Parcels

Given an `R * C` grid. Some cells contain post offices, and you can build exactly one new post office. Minimize the maximum distance from any cell to its nearest post office, where distance is defined as Manhattan distance.

Intuition:
I only came up with a brute-force solution, which passed the small case.
Start from each post office and update the distance of every cell.
When adding the new post office, enumerate every possible position.
Time complexity: O((R * C)^2)
Space complexity: O(R * C).

```cpp
#include <iostream>
#include <algorithm>
#include <string>
#include <vector>

using namespace std;

// 这里函数名其实应该是 dfs
void bfs(vector<vector<int>>& distance, int R, int C, int i, int j, int depth) {
	distance[i][j] = depth;
	vector<int> di = { -1, 0, 1, 0 };
	vector<int> dj = { 0, 1, 0, -1 };
	for (int k = 0; k < 4; k++) {
		int ni = i + di[k];
		int nj = j + dj[k];
		if (ni < R && nj < C && ni >= 0 && nj >= 0 && distance[ni][nj] > depth + 1) {
			bfs(distance, R, C, ni, nj, depth + 1);
		}
	}
}

int solution(vector<string> &grids, int R, int C) {

	vector<vector<int>> distance(R, vector<int>(C, numeric_limits<int>::max()));
	for (int i = 0; i < R; i++) {
		for (int j = 0; j < C; j++) {
			if (grids[i][j] == '1') {
				bfs(distance, R, C, i, j, 0);
			}
		}
	}

	int ret = numeric_limits<int>::max();
	for (int i = 0; i < R; i++) {
		for (int j = 0; j < C; j++) {
			if (grids[i][j] == '0') {
				auto distance_copy = distance;
				bfs(distance_copy, R, C, i, j, 0);
				int max_distance = 0;
				for (int k = 0; k < R; k++) {
					max_distance = max(max_distance, *max_element(distance_copy[k].begin(), distance_copy[k].end()));
				}
				ret = min(ret, max_distance);
			}
		}
	}

	return (ret == numeric_limits<int>::max()) ? 0 : ret;
}

int main() {
	int T;
	cin >> T;

	for (int i = 0; i < T; i++) {
		int R, C;
		cin >> R >> C;
		vector<string> grids(R);
		for (int j = 0; j < R; j++) {
			cin >> grids[j];
		}
		cout << "Case #" << i + 1 << ": " << solution(grids, R, C) << endl;
	}
}
```

After the contest, I studied the official analysis. The correct solution is:

First consider this subproblem: given a maximum distance `K`, can we add one post office so that the maximum distance is at most `K`?
This is a decision problem.
We can use my solution, but we only need to care about cells whose maximum distance is greater than `K`. Using this difference greatly improves the final efficiency.
Once we have a solution to the decision problem, we can binary search for the optimal answer. This is also a way to transform an optimization problem into a decision problem.

The overall approach is:
1. First compute the distance from every cell to its nearest post office. This can be done with BFS starting from all post offices. Since each cell is visited once, the time complexity is `O(R * C)`.
2. For all cells whose distance to a post office exceeds `K`, we need to determine whether there exists a cell whose distance to all of these cells is at most `K`. To implement this efficiently, note the equivalent formula for Manhattan distance: `dist((x1, y1), (x2, y2)) = max(abs(x1 + y1 - (x2 + y2)), abs(x1 - y1 - (x2 - y2)))`. For fixed point `(x2, y2)`, the maximum Manhattan distance is reached when `x1 + y1` or `x1 - y1` is maximized or minimized. For all cells whose maximum distance to a post office exceeds `K`, compute the maximum and minimum values of `x1 + y1` and `x1 - y1`, giving four values. Then try every possible cell where we can place the post office. Each cell can be checked in constant time, so this step is `RC + RC = O(RC)`.
3. Binary search. The total time complexity is `O(RC log(R+C))`.

```cpp
#include <algorithm>
#include <iostream>
#include <numeric>
#include <queue>
#include <set>
#include <string>
#include <utility>
#include <vector>

using namespace std;

class Solution {
  vector<set<pair<int, int>>> manhatten; // [i] 表示距离为i的点的集合
  vector<int> max_x_sub_y;
  vector<int> min_x_sub_y;
  vector<int> max_x_add_y;
  vector<int> min_x_add_y;
  void bfs(vector<string> &grids, int R, int C) {
    vector<vector<bool>> visited(R, vector<bool>(C, false));
    queue<pair<int, int>> q;
    for (int i = 0; i < R; ++i) {
      for (int j = 0; j < C; ++j) {
        if (grids[i][j] == '1') {
          visited[i][j] = true;
          q.push({i, j});
        }
      }
    }
    int level = 0;
    while (!q.empty()) {
      int s = q.size();
      for (int i = 0; i < s; ++i) {
        auto current = q.front();
        manhatten[level].insert(current);
        q.pop();
        vector<int> di = {-1, 0, 1, 0};
        vector<int> dj = {0, -1, 0, 1};
        for (int k = 0; k < 4; ++k) {
          int ni = current.first + di[k];
          int nj = current.second + dj[k];
          if (ni >= 0 && ni < R && nj >= 0 && nj < C &&
              visited[ni][nj] == false) {
            visited[ni][nj] = true;
            q.push({ni, nj});
          }
        }
      }
      ++level;
    }
  }

  bool possible(vector<string> &grids, int K, int R, int C) {
    int grids_count_larger_than_K =
        accumulate(manhatten.cbegin() + K + 1, manhatten.cend(), 0,
                   [](const auto &lhs, const auto &rhs) -> int {
                     return lhs + rhs.size();
                   });
    if (grids_count_larger_than_K == 0)
      return true;
    int max_x_sub_y_K =
        *max_element(max_x_sub_y.cbegin() + K + 1, max_x_sub_y.cend());
    int min_x_sub_y_K =
        *min_element(min_x_sub_y.cbegin() + K + 1, min_x_sub_y.cend());
    int max_x_add_y_K =
        *max_element(max_x_add_y.cbegin() + K + 1, max_x_add_y.cend());
    int min_x_add_y_K =
        *min_element(min_x_add_y.cbegin() + K + 1, min_x_add_y.cend());

    for (int i = 0; i < R; ++i) {
      for (int j = 0; j < C; ++j) {
        if (grids[i][j] == '0') {
          int distance = numeric_limits<int>::min();
          distance = max(distance, abs(i - j - max_x_sub_y_K));
          distance = max(distance, abs(i - j - min_x_sub_y_K));
          distance = max(distance, abs(i + j - max_x_add_y_K));
          distance = max(distance, abs(i + j - min_x_add_y_K));
          if (distance <= K)
            return true;
        }
      }
    }
    return false;
  }

public:
  int solution(vector<string> &grids, int R, int C) {
    manhatten.resize(R + C);
    bfs(grids, R, C);
    max_x_sub_y.resize(R + C);
    min_x_sub_y.resize(R + C);
    max_x_add_y.resize(R + C);
    min_x_add_y.resize(R + C);
    for (int i = 0; i < manhatten.size(); ++i) {
      max_x_sub_y[i] = numeric_limits<int>::min();
      min_x_sub_y[i] = numeric_limits<int>::max();
      max_x_add_y[i] = numeric_limits<int>::min();
      min_x_add_y[i] = numeric_limits<int>::max();
      for (const auto &point : manhatten[i]) {
        max_x_sub_y[i] = max(max_x_sub_y[i], point.first - point.second);
        min_x_sub_y[i] = min(min_x_sub_y[i], point.first - point.second);
        max_x_add_y[i] = max(max_x_add_y[i], point.first + point.second);
        min_x_add_y[i] = min(min_x_add_y[i], point.first + point.second);
      }
    }

    int lo = 0, hi = R + C;
    auto binary = [&grids, R, C, this](int K) -> bool {
      return this->possible(grids, K, R, C);
    };
    // [lo, hi)
    while (lo < hi) {
      int mid = lo + (hi - lo) / 2;
      if (!binary(mid)) {
        lo = mid + 1;
      } else {
        hi = mid;
      }
    }

    return lo;
  }
};

int main() {
  int T;
  cin >> T;

  for (int i = 0; i < T; i++) {
    int R, C;
    cin >> R >> C;
    vector<string> grids(R);
    for (int j = 0; j < R; j++) {
      cin >> grids[j];
    }
    cout << "Case #" << i + 1 << ": " << Solution().solution(grids, R, C)
         << endl;
  }
}
```
<!-- 
Further optimize to O(RC). In step 2, we can compute the maximum and minimum values for all cells. Then, by iterating over every possible cell, we get the optimal post-office location and distance. -->

## 3. Contention

Problem statement:
Given `Q` bookings and `N` seats. Each booking is represented by `L` and `R`, meaning it requests seats from `L` to `R`, inclusive.
For later bookings, if earlier seats are already occupied, they only take the seats that are still available.
Find the maximum `K` such that there exists an ordering of the bookings where every booking is satisfied with at least `K` seats.

Constraints: `Q = 30000`, `N = 10^6`

Official analysis:

Observation: given a booking order, the number of seats obtained by the last booking does not depend on the ordering of the previous bookings.
Therefore, we can determine the last booking each time and keep moving backward. The answer is the minimum number of reserved seats across the `Q` steps.

Another observation: when moving backward each time, we can greedily choose the last request from the remaining bookings: choose the request that can obtain the most seats. The intuition behind the greedy proof is that the final answer is non-increasing as we move backward.

This problem is still very difficult. During the contest, only two people solved it. I spent half a day on it and still could not understand it well, so I will leave the solution write-up aside for now.
