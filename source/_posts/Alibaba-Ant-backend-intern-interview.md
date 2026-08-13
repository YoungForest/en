---
title: Alibaba Ant Financial Backend Development Summer Intern
date: 2020-03-09 16:45:44
tags:
- Alibaba
- intern
categories:
- interview
translations:
  zh-CN: https://youngforest.github.io/2020/03/09/Alibaba-Ant-backend-intern-interview/
  en: https://youngforest.github.io/en/2020/03/09/Alibaba-Ant-backend-intern-interview/
---
# First Interview

time: 2020-03-09 16:45:44

## Resume Experience

Introduced each project experience. The interviewer asked questions at any time based on the projects, such as knowledge around RESTful API, SOAP, and so on.

## Computer Science Fundamentals

Implementation of HashMap

- How is a hash value mapped into buckets????
- Requirements for the `hashCode` and `equals` functions. If you modify `equals`, why must you also modify `hashCode`?
- Expansion mechanism and amortized complexity

Java unboxing and boxing mechanism. At first I did not react and said I did not know. With the interviewer's hint, I finally understood that it was about the relationship between primitive types and object types. Because the materials I had read before were all in English, `box` and `unbox`, I was not very sensitive to the Chinese terms.

## Algorithm Questions

The recurrence formula for edit distance.

K-means algorithm. I did not answer it well. I had studied machine learning a long time ago, but because I had put it aside for too long, I had basically forgotten it.

## Questions for the Interviewer

My question for the interviewer: the position I applied for is backend development. Do I need to review machine learning algorithms?

Answer: this depends on the company's product line. Ant Financial's products are closely related to machine learning. Development does not require deep understanding of machine learning, but it does require basic understanding. If you know it, it is a plus.

# Second Interview

time: 2020-03-24 16:20:56

It had already been two weeks since the first interview. Yesterday I received a call and scheduled a phone interview for 3 p.m. today. It lasted half an hour.

SenseTime internship project.

Implementation of ArrayList, dynamic array, and the difference between linked lists and arrays at the underlying level.

There are 10 million numbers in total, 10M, but only 2M memory. Find the 10 numbers that appear most frequently.

Answer: first split them into groups. You can divide them into small groups according to certain digits of the numbers, then find the 10 numbers in each group. Then merge and find the overall top 10 numbers.

What books have you read?

What does being familiar with SQL mean?

Concurrency knowledge:

- ConcurrencyHashMap
- lock-free programming

Career planning.

Overall I probably did not answer very well. The interviewer urged me to complete the written test, otherwise he could only ask about the content on my resume. After the written test, someone would contact me again. I was not in a very good state this afternoon and did not perform well. There was no hand-written coding section. Bad review~

# Written Test

Alibaba has multiple written tests. The one I took was on 3.25.

The written test was not difficult. It was done on NowCoder, with two algorithm questions, both around LeetCode medium difficulty. I got full marks, which seemingly made Alibaba think I was quite good. The later process went very smoothly, and they especially wanted me to join. Many colleagues and leads added me on WeChat and called to persuade me. So when I finally rejected Alibaba's offer, I actually felt pretty embarrassed.

>
> Given a 3 x n matrix, choose one number from each column to form the final array.
> Make the final

>$$\\sum_{i = 0}^{n-1} |b_{i+1} - b_i|$$
> as small as possible.
> Output this minimum value.

Just solve it with DP.

Time complexity: O(3N),
Space complexity: O(3).

```cpp
#include <vector>
#include <iostream>
#include <algorithm>

using namespace std;
using ll = long long;

int main() {
    int n;
    cin >> n;
    vector<ll> last_dp(3, 0);
    vector<vector<ll>> matrix(3, vector<ll> (n));
    for (int i = 0; i < 3; ++i) {
        for (int j = 0; j < n; ++j) {
            cin >> matrix[i][j];
        }
    }
    for (int i = 1; i < n; ++i) {
        vector<ll> dp(3);
        for (int dp_index = 0; dp_index < 3; ++dp_index) {
            dp[dp_index] = abs(matrix[dp_index][i] - matrix[0][i-1]) + last_dp[0];
            for (int j = 1; j < 3; ++j) {
                ll new_value = abs(matrix[dp_index][i] - matrix[j][i-1]) + last_dp[j];
                if (new_value < dp[dp_index]) {
                    dp[dp_index] = new_value;
                }
            }
        }
        last_dp = move(dp);
    }
    cout <<  min({last_dp[0], last_dp[1], last_dp[2]}) << endl;
    return 0;
}
```

-----

> A matrix of n x m, where each row and each column is an arithmetic progression, and the common difference is an integer.
> Input n x m numbers. If a number is 0, it means unknown; otherwise it is known.
> There are q queries asking whether the value at a specific position, using 1-based indices, can be determined.

Solve it recursively and infer values. You can use some arrays to maintain: whether each row/column has already been inferred, the index of the known number in each row/column, and row/column information.

Time complexity: O(N * M),
Space complexity: O(N * M).

```cpp
#include <vector>
#include <iostream>
#include <algorithm>
#include <functional>

using namespace std;
using ll = long long;
const int INF = 0x3f3f3f3f;

int main() {
    int n, m, q;
    cin >>  n >> m >> q;
    vector<vector<int>> A(n, vector<int> (m, INF));
    vector<int> row(n, -1);
    vector<bool> row_ok(n, false);
    vector<int> col(m, -1);
    vector<bool> col_ok(m, false);
    function<void(int, int, int)> fill = [&](int i, int j, int value) -> void {
        A[i][j] = value;
        if (col_ok[j] && row_ok[i]) {
            return;
        } else {
            if (!col_ok[j]) {
                if (col[j] == -1 || col[j] == i) {
                    col[j] = i;
                } else {
                    col_ok[j] = true;
                    int diff = (A[i][j] - A[col[j]][j]) / (i - col[j]);
                    for (int r = i - 1; r >= 0; --r) {
                        fill(r, j, A[r + 1][j] - diff);
                    }
                    for (int r = i + 1; r < n; ++r) {
                        fill(r, j, A[r - 1][j] + diff);
                    }
                }
            }
            if (!row_ok[i]) {
                if (row[i] == -1 || row[i] == j) {
                    row[i] = j;
                } else {
                    row_ok[i] = true;
                    int diff = (A[i][j] - A[i][row[i]]) / (j - row[i]);
                    for (int c = j - 1; c >= 0; --c) {
                        fill(i, c, A[i][c + 1] - diff);
                    }
                    for (int c = j + 1; c < m; ++c) {
                        fill(i, c, A[i][c - 1] + diff);
                    }
                }
            }
        }
    };
    for (int i = 0; i < n; ++i) {
        for (int j = 0; j < m; ++j) {
            int v;
            cin >> v;
            if (v != 0) {
                fill(i, j, v);
            }
        }
    }
    for (int i = 0; i < q; ++i) {
        int r, c;
        cin >> r >> c;
        --r;
        --c;
        if (A[r][c] == INF) {
            cout << "Unknown" << endl;
        } else {
            cout << A[r][c] << endl;
        }
    }
    return 0;
}
```

# Assessment

The written test did not defeat me, but the assessment took me quite a bit of time.

It mainly tested:

- Chinese: reading comprehension, idiom usage, summarization and elimination, 10 min
- Math: tables, charts, economic terminology, 10 min
- IQ: finding patterns from images, 10 min
- Personality: stress resistance, antisocial tendency, 30 min

It all relied on the knowledge and skills I learned in high school. IQ is just natural, I guess.

I heard it is very similar to the civil service exam, the administrative aptitude test. Friends who know about it can take a look.

They probably would not use the assessment to filter people anyway. This is not the civil service exam. Hand-written coding is the real way.

# Third Interview

time: 2020-04-03 11:36:14

Yesterday they called to schedule today's phone interview at 11:30. We talked for 40 minutes.

Yesterday afternoon I took Alibaba's online written test, and in the evening I did the assessment.

The interviewer was very NICE and even added me on WeChat for communication.

We mainly talked about projects, as well as some data processing knowledge and my understanding of Java.

- data preprocessing, handling missing values
- given credit evaluation data, how to use it and put it online
- degree of understanding and usage of Java, Spring Boot

At the end, he asked how my English was and how my spoken English was. Maybe it was because the other side was the international business group.

I finally asked about the interviewer's team and business.

It was personal credit loan business for overseas markets, similar to Huabei and Jiebei.

# Fourth Interview

time: 2020-04-03 15:22:02

Last week HR scheduled this week's cross interview with me. It was originally a video interview on Monday, but the interviewer went on a business trip, so they changed to another interviewer and rescheduled to Wednesday (4.1). Also, the new interviewer was very busy and did not set a specific time. He only asked me to wait for a call in the afternoon and evening. I received the call at 7:30 p.m. and talked for 40 minutes.

Because it was only a phone interview, there was no hand-written coding. He only asked one simple algorithm question: bucket sort.

Other than that, everything was about projects, but the project questions went very deep and into a lot of detail. The internship projects on my resume all took a lot of effort at the time, but too much time had passed, so I could not answer many details and considerations immediately. Also, when I was doing the internships, I focused more on the difficulties right in front of me, rather than deeply understanding the entire company's architecture, so my answers were not very good. The interviewer asked many BQ (Behavior Question) questions, such as:

- What was the biggest difficulty you encountered in previous projects?
- What was the saddest thing that happened during your internship?
- What is the thing you have persisted in doing for the longest time?

He asked in great detail. Many of these questions were things I had never encountered and had never seriously thought about. I had previously heard that only foreign companies had BQ, and most of them were asked by HR. So although there was no hand-written coding, the whole process was still very tiring.

At the end, the interviewer asked: do you have any other questions? I actually said, "No more questions. My parents are calling me to eat!" Such brutally honest words. Haha~

Yesterday I received another call and scheduled the HR interview for next Tuesday (4.7). Hope everything goes well.

What worries me most now is that because of the pandemic, I might not even have a summer vacation anymore, and I may not be able to go to the summer internship either.

Google's interview process has also stopped for three weeks. They said it was because of adjustments to the intern hiring policy, and it would be delayed until mid-April. I heard that the pandemic in the US is very serious, and many issued offers were rescinded. Could Google China also be reducing HC? Does any classmate know internal information? I am suffering!
