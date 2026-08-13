---
title: How Recommendation Systems Transform and Enslave Your Thinking
description: "How recommendation systems in popular apps shape attention, preferences, and the information we consume."
tags:
  - Tech
date: 2018-07-3 22:55:55
categories:
translations:
  zh-CN: https://youngforest.github.io/2018/07/03/recommender-systems-of-popular-apps/
  en: https://youngforest.github.io/en/2018/07/03/recommender-systems-of-popular-apps/
---
At Yuanzi's invitation, I wrote an article researching and discussing the recommendation systems used by today's popular content apps.
Here is how it started:
Yuanzi is a heavy Zhihu user. For a while, she spent more than an hour every day browsing Zhihu, and her worldview was deeply influenced by it.
I reminded her:

    It is not that Zhihu actively transforms your worldview;
    rather, based on Zhihu's recommendations, the more you browse, the more your existing tendencies are reinforced.
    For example, if you are relatively right-leaning, the recommendation system will eventually push you things you are willing to click into.
    The goal of a recommendation system is to increase the time users spend on the product, not to solve users' problems. Isn't that a bit scary when you think about it?
    This kind of recommendation system means that in the end, you only see what you already want to see.

Yuanzi suddenly became very interested in recommendation systems and "took revenge" on me:

    How about I give you an assignment? Since you have been researching recommendation systems recently, why don't you look up some materials and see how the recommendation systems of today's major content apps are built, what ideas they use, and organize it into an article?

<!-- more -->

This pushed me to organize my internship experience from the past two months and read a large amount of public material.
The result is this research article on the recommendation systems of major content apps.
The target audience of this article is students who deal with recommendation systems every day, by using major content apps, but know nothing about recommendation systems.

First, based on app download volume and their influence on Yuanzi, I think the major content apps include:

- Zhihu
- NetEase Cloud Music
- Kuaishou
- Taobao

This article also uses many examples from overseas companies such as Netflix, Amazon, Google News, and Facebook to explain recommendation systems. Although domestic students may not be familiar with these apps, they are leaders in the recommendation system industry.

## Two Major Types of Recommendation Systems

### Content-Based Filtering

This type of filtering depends only on item data.
In a recommendation system, what is recommended and consumed is the item. For example, in a news client like Toutiao, a news article is an item; on Zhihu, an answer; on NetEase Cloud Music, a song; on Kuaishou, a short video; on Taobao, a product.

Based on questionnaire information filled in by users, for new users, or based on user ratings, for old users who have produced many ratings or other actions reflecting their interests, if we discover that a user is interested in folk music, we continue recommending other folk songs they have not consumed.
Here, folk music is just a tag representing the song type. Besides that, tags such as singer name may also be used.

### Collaborative Filtering

This type of filtering depends on data from other users.
Collaborative filtering is based on the assumption that things liked by users similar to you may also be liked by you.
This is like how something recommended by your friend is more likely to match your taste than something recommended by a stranger. Your friend is, to some degree, similar to you and shares some common labels with you. According to psychological research, people also tend to become friends with people more similar to themselves.

In short, collaborative filtering works by finding a group of users similar to you and recommending items they liked or consumed to you.
There are many definitions of "similar"; generally, both basic user information and behavioral information are considered.
For example:
If your basic information says "24-year-old woman," Netflix may recommend films watched frequently by 24-year-old women.
On Amazon, behavioral information appears as: "You bought A, and most users who bought A also bought B. Do you want to buy B?"

When there are a large number of users, collaborative filtering performs better than content-based filtering.
The reason can be attributed to this: users themselves cannot precisely define what they like, but the body is honest. This can also be described as big data understanding you better than you understand yourself.
With the help of collaborative filtering, an app becomes better and more comfortable the more you train it, and you spend more and more time on it.
This creates very strong stickiness for old users. But conversely, after you stop using the app for a while, you will find that its recommendations no longer fit your taste as well as before.

Of course, content-based filtering is also useful, especially when a new user has just logged in and has not yet produced many actions or enough basic information to find similar people.
Attracting new users is very important, especially during a period of rapid app growth.
Correspondingly, the industry uses one-day retention rate, three-day retention rate, and seven-day retention rate to measure how attractive a recommendation system is to new users.

## Recommendation Systems Used by Major Content Apps

The main recommendation algorithms used by different companies basically belong to the broad category of collaborative filtering. Because application scenarios differ, such as news, products, music, and movies, the specific algorithms also differ.
To handle cold start, giving new users good recommendations when they first use the app, each company also uses content-based filtering and rule-based recommendation.
Because recommendation systems are a core competitive advantage for content-based companies, nothing fully public exists.
Also, according to one industry expert, the recommendation algorithms used by each company are basically the same, all from public algorithms; the differences are only in parameters, data, and rules. The final recommendation results also tend to converge.
We can only make guesses under different application scenarios, such as what kind of recommendation algorithm NetEase Cloud Music might use for music content.

### Zhihu

Zhihu's content is mainly text, though it has now added a lot of video content.
As a Q&A community for a "high-quality" crowd, Zhihu's question categories and descriptions are relatively clear, which helps recommendation.
Analyzing the content Zhihu recommends to me on the recommendation page, its recommendations are mainly answers, with a small number of Lives and ads in addition.
Because I do not know much about Lives, here I mainly speculate about the recommendation process for "answers."
At first, Zhihu's homepage mixed Recommendations and Following together. Now they are separated, and a Hot page has been added.
From the prompt on the recommendation page, "Follow topics to get precise content recommendations," we can see that the recommendation page is mainly based on the topics you follow. This is equivalent to a content-based filtering scenario.
From the results, most answers recommended to me are from topics I explicitly chose to follow or related topics, as shown in Figure 2 below: topics can have parent-child, related, and other relationships.

![Recommendation page](/assets/zhihu-recommend-page.jpg)

![Related topic page](/assets/zhihu-topic-page.jpg)


### NetEase Cloud Music

NetEase Cloud Music recommendations can be divided into three modules: Private FM, Daily Recommendations, and Recommended Playlists.

![NetEase Cloud Music recommendation modules](/assets/netease-music-homepage.jpg)

Private FM has three main actions:
- like
- delete
- skip

In addition, there are some other implicit actions that can be used, such as listening to a song all the way through, adjusting the progress bar, and time spent reading comments.
We can divide these behaviors into two broad categories: liking and disliking.
Depending on degree, different actions can be assigned different preference values.
For example, explicitly clicking Like and listening to the whole song both represent liking it, but the former is stronger and can be assigned a higher preference value.
All actions produced by a user on a played song are finally summed to obtain the user's degree of preference for that song.
After a user listens to Private FM for a period of time, they produce a series of actions.
Then we can obtain the user's preference degrees for all songs they have listened to.
Using this information, and then applying some classic collaborative filtering algorithms, we can recommend songs the user has not heard before.
So, the more you listen to Private FM, the better the recommendation results become.

Daily Recommendations are generally related to songs you have listened to recently. That is, a song interest's influence on today's recommendations decays relatively quickly over time.
For example, if you listened to electronic music yesterday, liked it, listened to several songs in a row, and perhaps "liked" some of them, today's Daily Recommendations may push more electronic music to you.

Recommended Playlists are relatively easy to recommend. Playlists are built by experts or enthusiasts, grouping many songs according to certain criteria. Each playlist also has its own tags, such as rock, Britpop, alternative, and indie. These tags are highly discriminative.
In the playlists recommended to me, there are usually several songs I "like." From this, we can see that Recommended Playlists use information from "my liked music." If several songs you like appear in the same playlist, it is highly likely that you will also like other music in that playlist.

### Kuaishou

As a short-video platform, Kuaishou produces a large number of videos every day. Videos are relatively hard to recommend because:

- machine understanding of semantics inside videos is still difficult
- almost all recommended content is cold-start content, meaning the system has to try to recommend newly uploaded videos

Under these conditions, Kuaishou may use tags as the main basis for recommendation.
Videos uploaded by users can be tagged by users themselves with all kinds of tags: shehuiyao, stunts, tutorials, snacks, and so on.
Relying on these tags, users and videos can be clustered into different groups. This way, newly uploaded videos can use actions generated by users on old videos.

### Taobao

As a C2C e-commerce platform, Taobao's recommendation system metrics differ from those of other B2C e-commerce platforms, such as Amazon.
One metric for evaluating whether a recommendation system is good is its ability to discover unpopular products, that is, to recommend the tail end of the [long tail](https://baike.baidu.com/item/%E9%95%BF%E5%B0%BE%E6%95%88%E5%BA%94).
Using a recommendation system to connect merchants of unpopular products with customers is one of Taobao's selling points, because there are simply too many products on Taobao. It is not that you cannot buy something; it is that you cannot imagine it.
Based on public information from Amazon, the core of an e-commerce recommendation system can be summarized in one sentence: people who bought product A also bought B; do you want to buy B?

## Why Recommendation Systems Transform and Enslave Your Thinking

For a content app's recommendation system, the final metric of whether recommendations are good is reflected in the amount of time users stay in the app.
When the number of internet users tends toward saturation, the total market size of user time will no longer grow as quickly as before. How to compete for users' precious time has become the goal of every content recommendation system.
To make you spend more time in their apps, major content apps have gone to great lengths.
For example, Zhihu now guides users from the mobile web into the app. Previously, even users who were not logged in could enjoy all information on the mobile web.
Now, on the mobile web, you can only see the beginning of an answer. If you want to browse the full content, you must download and enter the Zhihu app.
This greatly reduces user experience. For example, my friends often share Zhihu answers or questions with me, and I have to click several times to open them in the app before I can see the full information, which is very annoying.
Toutiao was also repeatedly called in for talks by the government because of vulgar content. Compared with other news, lowbrow and pornographic content is more likely to attract netizens' attention.
As mentioned at the beginning of this article, recommendation systems aim to increase user dwell time. They only blindly cater to user preferences, regardless of whether those preferences are correct.
The whole process is a positive feedback loop. If your thinking is relatively right-leaning, the recommendation system will push content that fits your taste and deepen your interest.
For an adult, there is still some ability to distinguish content and choose platforms.
For the vast number of minors, this is undoubtedly misleading. In addition, current regulation of internet content is not very complete. For profit, what recommendation systems push to minors will not be positive energy, but entertainment-oriented or even vulgar content. This has a very bad influence on the formation of minors' thinking.

In fact, how mature are many adults? Think about the content recommended to you, and think about the time you spend on these content apps.
Here I quote an old saying: "Good medicine tastes bitter but cures the disease; honest advice jars on the ears but benefits one's conduct." A recommendation system, however, only says pleasant things, the things you like to hear.
Subtly and imperceptibly, your thinking has already been transformed and enslaved by recommendation systems.

Relying on content from recommendation systems is also a form of laziness. Compared with actively acquiring information, directly accepting content from a recommendation system is much easier and more pleasant. But remember that laziness has a cost: your thinking will inevitably become narrow.

## How to Avoid the Bad Effects of Recommendation Systems

### Read More Books

Whatever illusion Zhihu gives you, books still have much higher content quality.
I recently read Binka's *One-Square-Meter Fitness*. Compared with fitness answers on Zhihu, its content quality is much higher.
After all, compared with writing an answer online, publishing a book has a much higher threshold and requires much more effort.
Especially because books also carry original thoughts from before the internet. This is what Zhihu lacks. Of course, after repeated copying and reposting, many "Zhihuers" apply knowledge from books in their answers. But these are processed and taken out of context. There are also many cases where someone copies a passage from a book and receives many upvotes.

### Make Friends with Excellent People

Making friends with excellent people and talking with them is much more useful than spending time following the words of big influencers.
I always think that what a person presents to you on the internet is only the part they want you to see, not the complete person.
By comparison, although face-to-face communication usually also hides one's true nature, that information exchange is bidirectional, not one-way output through a follow relationship.
Also, I wonder whether everyone has this feeling: when talking with excellent people, you spend more energy thinking, rather than just listening and being done. On platforms like Zhihu, the heat after reading does not last long.

### Only Exposure to Different Content Can Shape a Complete Worldview

Do not only read what you like; listen to different voices as well. Forming factions and attacking outsiders is human nature. If you do not deliberately avoid this human weakness, recommendation systems will be like flattering treacherous ministers: they only say what you want to hear, and you may become like a foolish ruler in history, neglecting state affairs. Opening channels for advice and listening to unpleasant but loyal criticism are what wise rulers should do.

For example, when you hear an opinion different from yours, do not immediately think about refuting the other person. Try to think about why they would think that way.
Everyone's thoughts reflect their own experiences. Without corresponding experiences, it is hard to understand some different things. At this point, do not rush to refute the other person; instead, try to enrich your own experience.
The two points mentioned earlier, "read more books" and "make friends with excellent people," are both efficient ways to enrich your experience.

## Afterword

I hope everyone, as adults, can have an independent worldview and not be enslaved by recommendation systems and their own laziness.
