---
title: Creating Chinese and English Blogs with Hexo
date: 2021-09-07 17:50:49
categories:
- Tinkering
tags:
- Hexo
---

Since joining Amazon, I have had more exposure to English and foreign colleagues. The need to practice using English has grown stronger. Also, to connect more with the international world, I decided to create my own English blog.

## Steps

Use the Chinese blog as a template and create an English blog.

```bash
cp -r Hexo HexoEn
```

<!-- more -->

Delete the unused Chinese posts and resources from `HexoEn`.

Create a new repo on GitHub: "en".

Modify the `HexoEn` `_config.yml` configuration file. Note that the deployment branch is `gh-pages`, GitHub's default Pages branch. If yours is different, you need to go to the repo's `Setting->Pages` and configure your custom branch:

```yml
language: 
- en
deploy:
  type: git
  repo: git@github.com:YoungForest/en.git
  branch: gh-pages
theme_config:
  menu:
    home: / || fa fa-home
    about: /about/ || fa fa-user
    tags: /tags/ || fa fa-tags
    categories: /categories/ || fa fa-th
    archives: /archives/ || fa fa-archive
    中文: https://youngforest.github.io || fa fa-language # Icons can be searched on this site: https://fontawesome.com/v4.7/icons/
```

The Chinese Hexo `_config.yml` configuration file:

```yml
language: 
- zh-CN
- en
theme_config:
  menu:
    home: / || fa fa-home
    about: /about/ || fa fa-user
    tags: /tags/ || fa fa-tags
    categories: /categories/ || fa fa-th
    archives: /archives/ || fa fa-archive
    English: https://youngforest.github.io/en/ || fa fa-globe
```

This way, the Chinese and English blogs can link to each other from the menu.

Deploy the blog and see how it looks.

```bash
hexo generate -d
```

Push `HexoEn` to the remote as well for backup and synchronization. Note that this uses the same repo as `deploy`, but a different branch.

```bash
git remote remove origin
git remote add origin git@github.com:YoungForest/en.git
git push -u origin master
```

## Final Result

Chinese blog: https://youngforest.github.io
English blog: https://youngforest.github.io/en/
