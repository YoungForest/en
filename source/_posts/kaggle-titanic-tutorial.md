---
title: 'Getting Started with Kaggle: Titanic'
categories:
  - Kaggle
description: "A hands-on Titanic tutorial covering problem framing, data exploration, preprocessing, feature engineering, model evaluation, and submission."
date: 2018-08-14 11:22:23
tags:
translations:
  zh-CN: https://youngforest.github.io/2018/08/14/kaggle-titanic-tutorial/
  en: https://youngforest.github.io/en/2018/08/14/kaggle-titanic-tutorial/
---
To train my data mining ability more quickly, I plan to spend some time on Kaggle competitions over the next month. The current plan is to follow the competitions, Datasets, and expert notebooks under [Tutorial](https://www.kaggle.com/tags/tutorial) as an introduction. After that, I will choose a simple regular competition to participate in.
As for whether I will eventually follow Brother Huan onto the path of data mining competitions, that depends on how I feel after getting started and whether I am willing to continue spending a lot of energy on it.

Data mining ability is mainly divided into two parts:
- machine learning
- feature engineering

When I previously interned in the recommendation team at Kuaishou, the work also involved these topics. Recommendation systems can also be considered a branch of data mining. In terms of output, they predict whether a user will click a certain item.

[Titanic: Machine Learning from Disaster](https://www.kaggle.com/c/titanic), as most people's first step into Kaggle, really is a concentrated showcase of data mining skills. By contrast, the competitions that have recently been popular on Kaggle are mostly related to CV or NLP. They require more domain knowledge and dilute the importance of general data mining skills.
There are also many [other tutorials](https://www.kaggle.com/tags/tutorial) on Kaggle for users to study. In the future I will choose some of them for focused learning and try to get started with Kaggle before the semester begins.

<figure class="editorial-illustration editorial-illustration--hero">
  <img src="/en/images/ai/kaggle-titanic-tutorial/en-hero.webp" alt="Mixed passenger clues enter a data workshop and pass through gap repair, feature drawers, competing model machines, a validation scale, and a final submission envelope" width="1536" height="864" decoding="async">
</figure>

<!-- more -->
The Titanic training skills are:
- binary classification, alive or dead
- basic Python/R

[notebook link](https://www.kaggle.com/helgejo/an-interactive-data-science-tutorial/notebook) This is the notebook I followed this time.
I manually typed all the code and refused to copy and paste; I also completed all the Exercises that needed to be done. I gained an initial understanding of the general competition workflow:

- problem understanding
- data understanding
- data preprocessing
- machine learning modeling
- model evaluation
- submitting results

## Problem Understanding

In the problem description, Kaggle usually provides the dataset background, the goal of each challenge, and the required skills.
You can also judge what domain knowledge is needed. For example, in Titanic, knowledge of Western names in the early twentieth century, aristocratic titles, and ships is extremely helpful.

After reading the problem description, you can form basic guesses: what factors have the greatest impact on survival?
I would think of age and gender. Hasn't the media always said that when a ship sinks, women and children go first?
Later, we use data analysis to verify whether the guesses are reasonable.

## Data Understanding

With Python and its visualization tools, we can quickly get an initial understanding of the data.

For example, `titanic` stores all raw data as a `pd.DataFrame`. `titanic.head()` can display the first five rows, and `titanic.describe()` displays statistics for all features, including maximum, minimum, missing counts, mean, variance, and so on.
After gaining an intuitive understanding of all raw features, you can know which features are categorical, which are continuous, and which need preprocessing.

With a heat map, you can intuitively see correlations between different features.
For continuous values, draw their distribution charts with `plot_distribution()`; for categorical values, draw box plots with `plot_categories`.

With these skills, you can develop a clear sense of **which features to select for modeling** and **how to perform data preprocessing**.

## Data Preprocessing

For categorical variables, they need to be converted into numeric types before further modeling.
Binary categorical variables can simply be mapped to (0, 1).
Multi-class categorical variables need one-hot encoding with `pd.get_dummies()`.

**Fill missing values**. Because real-world data collection is uncertain, some rows have missing values in certain columns. At this point there are two choices: drop the entire row, or fill missing values with meaningful values.
When data is precious, because the dataset is small or there are too many missing values, filling is generally used. For categorical values, you can fill "Unknown"; for continuous values, you generally fill the mean.

**Feature engineering**. This is the skill that separates contestants of different levels. It requires domain knowledge and enough sensitivity to the data. However, because of deep learning this year, feature engineering has shown a trend of being abandoned. Differences between contestants mainly come from parameter tuning. In Titanic, if you understand the West in the early twentieth century well enough, you can do some feature engineering. For example, extract "Title" from names to identify whether someone is nobility, commoner, or staff. If you are familiar enough with ships, you can extract cabin class from cabins and ticket class from ticket numbers.

**Assemble the final dataset**. After preprocessing, the dataset needs feature selection and assembly into a form suitable for modeling. It is usually a `pd.DataFrame`, and it also needs to be split into Train, Valid, and Test sets.

## Modeling and Model Evaluation

This is also where a lot of work lies. Common machine learning models need to be tried one by one, and each model's parameters need to be adjusted and tried again. Then the validation set is used for evaluation, and the best model is selected.
Often, you also need to return to data preprocessing, select different features, perform different feature engineering, then come back for modeling and evaluation.

This part is the tedious experimental part, because it requires a lot of attempts and choices. Of course, you can write automation scripts to speed up this work.

## Submission

Submission is relatively simple. The main thing is to pay attention to the submission method and format. Competitions usually provide a submission template to help participants understand the required format.

My first submission received a score of 0 because the predicted values had not been converted from floating-point numbers to integers. After fixing that issue, I got a score of 0.77033 and ranked 6242/9939.
This submission counted as one experience of the whole workflow.
I will continue feature engineering and parameter tuning later to obtain a higher score.
The impressive thing is that many people have full scores. I previously heard there might be a bug leaking prediction information into features; I do not know whether that was the reason.

## Afterword

According to senior schoolmates looking for jobs this year and information online, competition for algorithm engineer roles is extremely intense this year. A large reason is the high salaries of algorithm roles and the sweet returns last year's graduates received. Everyone has been switching from development roles to algorithm roles. Many data mining competitions have also become very popular as a result, and Kaggle is the hottest competition platform among them. I also plan to ride the wave for one month and see whether I have the talent and ability to do algorithms.

On Kaggle, the first people I followed were Brother Huan, `mgchbot`, and my senior schoolmate, `sparkingarthur`. Looking at their profiles, they really are impressive people. Brother Huan in particular is worthy of being a double KDD Cup champion. My senior schoolmate is also strong, though he is usually very modest in front of me, perhaps because someone like Brother Huan is always around. Their Kaggle rankings are 100 and 1000 respectively, among more than 80,000 users at the time. Since I have not finished any competition yet, my ranking is Unranked. Learn from excellent people, especially with the temptation of a 400k+ hukou value.
