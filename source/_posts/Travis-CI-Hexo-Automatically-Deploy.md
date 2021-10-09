---
title: Travis CI + Hexo Automatically Deploy
date: 2021-10-09 11:03:49
tags:
- Blog
categories:
- Tech Sharing
---

[Hexo](https://hexo.io/docs/) is a powerful blog engine. It is a good choice for a personal blog. I have used it for 5 years. Recently, I setup [my English site](https://youngforest.github.io/en) besides [the original Chinese site](https://youngforest.github.io/). With 2 sites, it is more work to write, git and deploy.
Fortunately, there is a better solution for this problem. We could use [Travis CI](https://travis-ci.com/) to automatically deploy the blog. The remain work is writing posts and git commit and push them. The CI would help us to deploy sites automatically.

<!-- more -->

## How to configure that?

[Sign up for Travis CI using Github account](https://education.travis-ci.com/).

With [Github education package](https://education.github.com/pack/offers), you could have the benefit to private builds for free while you're a student. But it is only an optional choice, you can still use Travis CI without it.

[Give Travis CI the permission to access your Github repositories](https://app.travis-ci.com/account/repositories).

In [Personal access tokens](https://github.com/settings/tokens), generate a token for Travis CI with `repo` scopes. Copy the token.

In [Travis CI repositories page](https://app.travis-ci.com/account/repositories), select your **Hexo** repo and click on **Settings**. In **Environment Variables** section, **Add** a new variable with **NAME** is `GH_TOKEN`, **VALUE** is the token you copied in the last step.

Create a file named `.travis.yml` in your Hexo repo. Refer to following content and change the value as your situation. I added some comments for help.

```
os: linux
language: node_js 
node_js:
  - 12  # using nodejs LTS v12
branches:
  only:
    - master # only monitor master branch
cache:
  directories:
    - node_modules # cache node_modules to speed up build process
before_script: ## According to your theme and custom configuration, update the script
  - npm install -g hexo-cli # install Hexo in CI environment
  - cd themes 
  - git clone https://github.com/next-theme/hexo-theme-next.git next # We do not commit the themes/next folder. Therefore, pull it every time.
  - cd next
  - npm install # install next dependencies
  - cd ../.. # return the root folder
  - npm install # install the dependencies
script: 
  - hexo generate # generate static files
deploy:
  provider: pages
  skip_cleanup: true # not clean after build
  token: $GH_TOKEN # the variable you set in last step
  keep_history: true
  # fqdn: blog.ne0ng.page # custom domain，delete this field if using username.github.io
  repo: YoungForest/youngforest.github.io # the repo you want to deploy. If this field is not set manually, the default value is current repo. Sometimes, the site repo is the same with the source files but in different branches.
  on:
    branch: master # sources files is in master branch
  local_dir: public 
  target_branch: master # the branch of generated files. If using `username.github.io` repo, it has to be master. Otherwise, change it to your github pages branch.
```

Once you push the `.travis.yml` file to your repo, you can see the build status in [Travis CI](https://app.travis-ci.com/). Once it complete, check your site repo and access your blog to see the update.