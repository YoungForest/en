---
title: Migrate from Travis CI to Github Action
date: 2021-12-18 10:47:17
tags:
- Blog
categories:
- Tech Sharing
description: Migrating a Hexo blog deployment from Travis CI to GitHub Actions after free public-repository builds stopped working.
translations:
  zh-CN: https://youngforest.github.io/2021/12/18/Migrate-from-Travis-CI-to-Github-Action/
  en: https://youngforest.github.io/en/2021/12/18/Migrate-from-Travis-CI-to-Github-Action/
---

<figure class="editorial-illustration editorial-illustration--hero"><img src="/en/images/ai/Migrate-from-Travis-CI-to-Github-Action/en-hero.webp" width="1536" height="864" alt="Static-page crates cross from a dark stalled conveyor into a branching workflow machine that restores paired-site delivery" decoding="async" fetchpriority="high"></figure>

<!-- more -->

Two months ago, I configured Travis CI to automatically deploy my blog in
[Travis CI Hexo Automatically Deploy](https://youngforest.github.io/en/2021/10/09/Travis-CI-Hexo-Automatically-Deploy/).
However, I found it do not work this month. Unfortunately, Travis do not support free build for public repos now. I have to migrate to Github Action in order to auto CD.

I followed [this post](https://sanonz.github.io/2020/deploy-a-hexo-blog-from-github-actions/) to do that. Some changes should be applied in `deploy.yml` file, especially **theme**.
For example, [my Chinese blog `deploy.yml`](https://github.com/YoungForest/Hexo/blob/master/.github/workflows/deploy.yml) and [my English blog `deploy.yml`](https://github.com/YoungForest/en/blob/master/.github/workflows/deploy.yml).

Close auto build in [Travis CI Dashboard](https://app.travis-ci.com/): Your repos -> Setting -> General -> uncheck **Build pushed branches** and **Build pushed pull requests**.
