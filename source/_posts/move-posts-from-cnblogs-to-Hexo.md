---
title: Migrating Posts from CNBlogs to Hexo
tags:
  - Hexo
categories:
  - Tinkering
date: 2016-11-22 01:42:23
translations:
  zh-CN: https://youngforest.github.io/2016/11/22/move-posts-from-cnblogs-to-Hexo/
  en: https://youngforest.github.io/en/2016/11/22/move-posts-from-cnblogs-to-Hexo/
---
Regarding how to migrate posts from other blogs to `Hexo`, the [official documentation](https://hexo.io/docs/migration.html) provides some guidance. I also followed the official documentation to try migrating my previous posts from CNBlogs to the new blog. I encountered some problems during this process, so here are the solutions.
<!-- more -->
## Migration Plugin Not Installed

CNBlogs needs RSS migration. Following the documentation, I installed the related plugin with `$ npm install hexo-migrator-rss --save`, but when running the migration command `$ hexo migrate rss <source>`, the following error was reported:

``` bash
undefined migrator plugin is not installed.
Installed migrator plugins:

For more help, you can check the online docs: http://hexo.io/
```

I found the answer on [Stack Overflow](http://stackoverflow.com/questions/34025076/when-running-hexo-migrate-rss-it-prompt-undefined-migrator-plugin-is-not-insta). It turns out that the plugin installation command `$ npm install hexo-migrator-rss --save` needs to be run under the blog directory. This shows I was still unfamiliar with Node.js, because Node.js package management installs locally by default, under the current folder.

After reinstalling the `hexo-migrator-rss` plugin under the blog directory, the problem was solved.

## Migrated Posts Only Have Summaries and Hyperlinks
After the migration succeeded, I excitedly checked the migration result immediately. It turned out that the migrated posts only had summaries and hyperlinks to the original CNBlogs posts, just like the image below. This was clearly not what I wanted.

![Incomplete migration](https://cloud.githubusercontent.com/assets/13612111/20493077/5f16663e-b052-11e6-9a0f-256d35ca6110.png)

This happened because the RSS exported by CNBlogs is exactly like that. Through observation, I found that the RSS link is only an `xml` file containing article information. In other words, if we can obtain an `xml` file with full article information, it should work. It happens that CNBlogs' backup function can provide such a file. Replacing the RSS link with the backup file link and running the migration command again can migrate the complete articles into Hexo. Below is the effect of complete migration.

![Complete migration](https://cloud.githubusercontent.com/assets/13612111/20493198/cc799b42-b052-11e6-9a0d-28f2e3f8e207.png)

However, this migration still lost information such as tags, so it was not completely satisfactory.

## Strange Formatting Problems
After successful migration, I found that the formatting of two posts became very strange. The main text was normal, but quoted sections and inserted code were laid out messily. Another post, however, was perfect. By observing the content of the migrated `xml` file, I found that the content of those two posts was in HTML format, while the other post was in `Markdown` format. Only then did I remember that the first two essays were written with the `TinyMCE editor` recommended by CNBlogs, and only later did I switch to `Markdown`. So of course Hexo could not support them perfectly.
