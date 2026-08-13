---
title: Work With Two Github Accounts Simultaneously
date: 2017-12-12 20:32:52
tags:
- Git
categories:
translations:
  zh-CN: https://youngforest.github.io/2017/12/12/work-with-two-github-accounts/
  en: https://youngforest.github.io/en/2017/12/12/work-with-two-github-accounts/
---
Three months ago, I came to [Oushu Technology](http://www.oushu.io/) for an internship. Both studying and working used the Mac provided by the company. You get what you pay for; the Mac really is much easier to use than the 5000 RMB HP laptop I bought in 2015. So besides work, my study tasks and lab tasks also gradually migrated to the Mac. Now I have encountered a rather tricky problem: how to keep my work GitHub account from conflicting with my personal account while still using both conveniently.

After looking through related materials, I implemented the following solution.

<!-- more -->
Use the company account as the global default, and do nothing special where the company account is needed.

Handle personal repositories specially.

1. Create an SSH key locally
2. Configure the SSH public key in personal GitHub settings
3. Modify `~/.ssh/config`
``` config
# Personal github user
Host github.ys
        HostName github.com
        User git
        IdentityFile ~/.ssh/id_rsa_personal_github
```
4. When cloning repositories, use the SSH link and replace `github.com` with `github.ys`
5. For existing repositories, change the `remote` item in `.git/config`; the link is the same as in step 4
6. Configure local commits.
``` bash
git config user.email yangsen758@foxmail.com
git config user.name YoungForest
```

To make `hexo` deploy automatically, change the repo link in Hexo's `_config.yml`, similar to cloning.
``` yml
# Deployment
## Docs: https://hexo.io/docs/deployment.html
deploy:
  type: git
  repo: git@github.ys:YoungForest/youngforest.github.io.git
  branch: master
```
