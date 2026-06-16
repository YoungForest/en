---
title: Building a Personal Blog with Hexo + GitHub
date: 2016-11-02 22:32:55
categories:
- Tinkering
tags:
- Hexo
mathjax: false
---

## Motivation, Placed First
I used to have a blog on CNBlogs, but I did not maintain it often. Even now, counting everything, it only had three essays and one follower. The main reason was that I did not have the habit of writing. In high school, I kept a diary for a while and wrote more than half a notebook in total. It was all motivational, self-deceiving, content written for myself.

<!--more-->

First, I used to feel that there was nothing to write about, but now I think there is actually quite a lot. I have tinkered with many things over the past two years. But to be honest, after finishing the tinkering, I forget things very quickly. The next time I do it myself or help someone else handle the same problem, I basically have to start over. After writing things down, whether for my own future needs or for friends to reference, it is extremely useful.

Second, after being strongly recommended by experts, I learned that frequently writing blogs, summarizing one's work and study, and communicating with experts online are basic skills for studying Computer Science. Writing things down not only systematically records my learning process for future review or for later people to reference, but also urges me to persist. Why not?

Finally, while recently working on the compiler course project, I saw [this blog post](http://jcf94.com/2016/02/21/2016-02-21-pl0/). While learning compiler knowledge, I felt that the blog design was very beautiful and the blogger was also very nice. After learning that it was built with `Hexo`, I had the idea of building my own blog, and now I finally have time to implement it.

## Introduction to `Hexo`
There are many frameworks specifically used to build blogs, such as WordPress, Jekyll, Octopress, Joomla, and so on. I have not used any of these, so I will not comment on them. In fact, this is my first experience building a blog, and in a way it was fate that I encountered Hexo.

Here I will talk about Hexo's advantages based on my own experience:

1. Complete documentation.
2. Good Chinese support, including all aspects of the documentation. In fact, the developer is Chinese.
3. Rich themes, important for someone who cares about appearance.

## Setup Process
### Local Hexo Installation and Configuration

For how to build it, I recommend the official [documentation](https://hexo.io/zh-cn/docs/). The documentation already explains things in detail. If you encounter problems, you can also ask directly in the discussion area at the bottom of the documentation. It seems many people encounter strange problems. I usually rely on Google when I encounter problems; in fact, many times Google leads me to the discussion area or the [Troubleshoot](https://hexo.io/zh-cn/docs/troubleshooting.html) section in the documentation.

The environment I used to install Hexo was Ubuntu 14.04. When installing Hexo with `$ npm install -g hexo-cli`, I encountered this problem:

```
sh: 1: node: not found
npm WARN This failure might be due to the use of legacy binary "node"
npm WARN For further explanations, please read
/usr/share/doc/nodejs/README.Debian
```

Through searching, I quickly found the [solution](http://stackoverflow.com/questions/21168141/cannot-install-packages-using-node-package-manager-in-ubuntu). You only need `sudo apt-get install nodejs-legacy`.

The reason for this problem is that the Debian community found that the command for the Node.js interpreter is `node`. However, this command conflicts in namespace with other packages, such as `ax25-node` in the `node` package. Therefore, they decided to change the Node.js interpreter command to `nodejs`; when people need to use the Node.js interpreter, they should use the `nodejs` command instead of `node`. But this creates a backward compatibility problem, because previous Node.js scripts still use the `node` command, and other Linux distributions have also always used `node`. So they came up with a compatibility method: create a symbolic link from `/usr/bin/node` to `/usr/bin/nodejs`. This work is handled by the `node-legacy` package, and users are not recommended to link it manually themselves. They also specified that the `nodejs` source package should also provide a `node-legacy` executable package. This "perfectly" solves the compatibility problem.

From this, it seems Debian-based distributions will all encounter this problem.

### Publishing to GitHub
Create a new repository on GitHub named `{your name}.github.io`; for example, mine is `youngforest.github.io`. In fact, with this naming format, GitHub will configure your `[GitHub Pages](https://pages.github.com/)` by default and publish it to `https://{your name}.github.io/`; mine is `https://youngforest.github.io/`.
Of course, if you do not name it this way, you can also configure `GitHub Page` publication in the repository settings yourself.

In the local blog directory, run `hexo generate`, and the static pages of your blog will be generated under `public/`. Under this folder, push all files to the repository you created on GitHub.

As for how to push a local folder to a newly created repository, GitHub should tell you when you create the empty repository.

![initial a repository in github](https://cloud.githubusercontent.com/assets/13612111/20461580/713552e6-af3d-11e6-8078-a8c570e594af.png)

If you want to complete GitHub deployment with one command, you can refer to the [related documentation](https://hexo.io/zh-cn/docs/deployment.html).

When I ran `hexo deploy`, I encountered this error: `ERROR Deployer not found: git`.

After searching for a long time, I found the solution in a [Hexo issue](https://github.com/hexojs/hexo/issues/1040). Just run the following commands in Bash.
``` bash
npm install hexo-generator-index --save
npm install hexo-generator-archive --save
npm install hexo-generator-category --save
npm install hexo-generator-tag --save
npm install hexo-server --save
npm install hexo-deployer-git --save
npm install hexo-renderer-marked@0.2 --save
npm install hexo-renderer-stylus@0.2 --save
npm install hexo-generator-feed@1 --save
npm install hexo-generator-sitemap@1 --save
```

When you visit your `GitHub Pages` in the browser, you will see the deployed blog.
You can see my deployed blog [here](https://youngforest.github.io/).

