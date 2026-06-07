---
title: Configuring Google AdSense in Hexo NexT
date: 2021-02-25 11:17:14
categories:
- Tinkering
tags:
- Hexo
---

## Motivation

Recently, after watching some videos by "Banfo Xianren", I became more interested in money. Besides taking out part of my savings to invest in A-shares, I also started thinking about how to increase passive income. I have always had the habit of writing a blog. I have been writing for more than five years and have accumulated over a hundred articles. Although daily page views are only in the dozens, even a small fly still has meat. Referring to some experiences from experts, I tried making money by placing ads.
Website ad revenue is generally connected through an ad network. In short, you only rent ad slots to the ad network. They place user-targeted ads there and pay you based on traffic.
For international audiences, the main option is Google AdSense. There are also some domestic options, such as Baidu.
Because my main job is as a programmer, and blog content is not limited by national borders, I chose Google AdSense as the ad provider. Google AdSense is very simple to configure.

## Specific Steps

[Register a new AdSense account](https://www.google.com/adsense/).

Get the ad script for your website from Google AdSense. It looks similar to this:

```html
<script data-ad-client="ca-pub-9*******6" async src="https://pagead2.googlesyndication.com/pagead/js/adsbygoogle.js"></script>
```

Option 1:

Paste the ad script at the end of `./themes/next/layout/_partials/head/head-unique.swig`.

Then verify it. Usually it will pass after a few days, and you will receive an email notification.

After approval, you need to configure it in AdSense. Basically, turning on **Auto ads** is enough.

Option 2:

Create a `_config.next.yml` file under the blog root directory, with the following contents:

```yml
custom_file_path:
  head: source/_data/head.swig
```

Create `source/_data/head.swig` with the following contents:

```html
<script data-ad-client="ca-pub-9046219176772396" async src="https://pagead2.googlesyndication.com/pagead/js/adsbygoogle.js?client=ca-pub-9046219176772396"></script>
```

The advantage of Option 2 is that it does not intrude into `next`, making it convenient to do CD with GitHub Actions.

### Setting up ads.txt

Place this file under `./source/`, at the same level as `_posts/`.
After deploying with `hexo d -g`, the corresponding file will be automatically deployed to `/public/`, meaning your blog site.
Other files that need to be generated and placed in the blog root directory can be handled similarly.
