---
title: Advanced SQL Practice
date: 2018-05-07 23:30:25
tags:
- Kuaishou
- Tech
categories:
- Intern
translations:
  zh-CN: https://youngforest.github.io/2018/05/07/Advanced-SQL-practice/
  en: https://youngforest.github.io/en/2018/05/07/Advanced-SQL-practice/
---
This week at the company I wrote many long and functionally complex SQL statements. I deeply felt that my database and SQL knowledge was still only superficial and completely unable to meet engineering requirements.
The second mentor responsible for guiding me is also quite proficient in SQL and gave me a lot of guidance and suggestions.
Without comparison, there is no hurt. Seeing how weak I was and how strong my senior schoolmate was made me feel ashamed and then determined, so I developed the idea and action plan of advancing my SQL skills.

<!--more-->
The two tutorials recommended to me, thanks to my Kuaishou senior schoolmate and second mentor:
- *Sams Teach Yourself SQL in 10 Minutes*
- [Runoob](http://www.runoob.com/sql/sql-constraints.html)

Among them, I systematically read *Sams Teach Yourself SQL in 10 Minutes* once, while I only roughly skimmed the Runoob tutorial.
I think reading one tutorial is enough.
The advantage of Runoob is that you can test and practice on the online database provided by the website, which is somewhat similar to the [w3school tutorial](https://www.w3schools.com/sql/).
The advantage of *Sams Teach Yourself SQL in 10 Minutes* is that it contains more material. The author also teaches some experience in writing SQL and touches on portability and differences across different databases.

There are [42 Database interview problems on LeetCode](https://leetcode.com/problemset/database/). I must practice them when I have time.

# Problems Encountered in Practice
Because my role is algorithm engineer in the recommendation team, there is a large amount of data mining work.
Also, what I write is not pure SQL, but [Hive](https://cwiki.apache.org/confluence/display/Hive/Home)-SQL.
Hive has many limitations and challenges. For example, you cannot nest a `select` clause inside `select`; `select 2 * (select 3);` is not allowed. The solution I finally found was to use aggregate functions or computable relationships between columns for the needed calculations, which really tests flexible use of `union` or `join`. The data volume is especially large, and each query is time-consuming. I learned to use small queries to verify ideas, validate layer by layer from the inside outward, and run queries and calculations in `jupyter notebook`.

# Answering Interview Questions
The interviewer in the first round asked me at the time: Do you know SQL?

Me: Yes, and I also previously had an internship at a database company.

Interviewer: Then tell me the differences between `left join`, `right join`, and `inner join`.

Me: I do not know...

Interviewer: Then do you know `partition`?

Me: I do not know that either.

Interviewer: Is your SQL knowledge limited to the Database Principles and Course Project classes you took?

Me: Yes. And because I had not used it for a while, I had also become a bit rusty.

Then the interviewer stopped asking SQL-related questions.

After a week of work and systematic relearning of SQL, I can now answer these two questions.

### Differences Between `left join`, `right join`, and `inner join`
 
`inner join` is an inner join. Non-matching items do not appear in the joined result. `left join` is a left outer join. If an item on the left side has no matching item in the right table, one record still exists in the joined result, and the columns from the right table are all `NULL`. `right join` is similar. In fact, `left join` and `right join` can be interchanged; the two joined tables just need to swap left and right positions.

### The Concept of `partition`

`partition` divides a table into different partitions to speed up queries.
The limitation is that during `select`, the partitioned column must be written in `where` as a filter.
