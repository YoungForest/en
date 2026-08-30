---
title: Launch IPython from a Different Conda Env
date: 2017-11-02 22:36:37
tags:
categories:
description: "How to register an IPython kernel for a separate Conda environment, launch the intended session, and use package mirrors for faster installs."
translations:
  zh-CN: https://youngforest.github.io/2017/11/02/Lunch-IPython-from-differnt-conda-env/
  en: https://youngforest.github.io/en/2017/11/02/Lunch-IPython-from-differnt-conda-env/
---
[reference](http://ipython.readthedocs.io/en/stable/install/kernel_install.html)

<figure class="editorial-illustration editorial-illustration--hero">
  <img src="/en/images/ai/Lunch-IPython-from-differnt-conda-env/en-hero.webp" alt="A shared notebook desk selects one sealed environment capsule through its matching kernel key while the other capsules remain isolated" width="1536" height="864" decoding="async" fetchpriority="high">
</figure>

<!-- more -->

``` python
# activate virtual python environment
activate python27

# install package ipykernel in virtual environment
pip install ipykernel

# install ipython kernel for virtual environment
python -m ipykernel install --user --name py27 --display-name "Python (py27)"

# lunch jupyter QTConsole with specific kernel
jupyter qtconsole --kernel=py27
```

## Using a Mirror Instead of Crossing the GFW
You have two choices to make it work inside the GFW.
- mirror, which I recommend for better speed
- VPN

[mirror site](http://mirrors.ustc.edu.cn/help/anaconda.html)

[Latest install package mirror site](https://mirrors.ustc.edu.cn/anaconda/archive/)

``` bash
# add package source mirror
conda config --add channels https://mirrors.ustc.edu.cn/anaconda/pkgs/free/
conda config --set show_channel_urls yes
```
