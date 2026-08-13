---
title: gVim configuration for Windows
date: 2017-03-26 18:46:41
tags:
- vim
categories:
- Tinkering
translations:
  zh-CN: https://youngforest.github.io/2017/03/26/gvim-configuration-for-windows/
  en: https://youngforest.github.io/en/2017/03/26/gvim-configuration-for-windows/
---
Recently, because I was preparing for the GRE, I spent all day memorizing words, practicing listening, and writing. I felt quite annoyed and had not tinkered with anything for a long time. So I took some time to download and configure gVim on my laptop, which also counted as a break.

# [Download](http://www.vim.org/download.php#pc)

# Configuration Files

Unlike Linux, the gVim configuration files on Windows are `$HOME/_vimrc` for the personal configuration file and `$VIMROOT/_vimrc` for the system configuration file. By default, `$VIMROOT` is `C:\Program Files (x86)\Vim`. Besides these, there can also be a `_gvimrc` file, which is only read when opened in GUI mode, not in the terminal. This is helpful when applying different configurations to the two modes, especially when using different themes. The same theme can look very different in GUI and terminal mode. This avoids complicated configuration such as `if(has'gui_running')`.

# `.vimrc` Configuration

## Open Configuration Files

+ `:e $MYVIMRC`: open the user configuration file. If it does not exist, refer to `help vimrc`.
+ `:e $MYGVIMRC`: open the GUI user configuration.

The configuration file locations recommended in `help vimrc` are `$HOME/vimfiles/vimrc` on Windows or `~/.vim/vimrc` on Unix-like systems. This is more portable than `$HOME/_vimrc` and `~/.vim`.

## Personalization

$MYGVIMRC
``` vim
set clipboard=unnamed   " 与Windows公用clipboard, 默认情况下, y, p只使用vim的clipboard, 不是很方便
set colorscheme monokai " 一个我比较喜欢的主题, 不过在terminal下很难看, 所以放在gvimrc中
set guifont=Consolas:h18:cANSI:qDRAFT   "换个字体, 默认字体忍不了
```

$MYVIMRC
``` vim
set number
set nobackup    "不产生~文件
set noswapfile  "不产生.swp文件
set noundofile  "不产生.un文件
set encoding=utf-8  "默认为cp936, 改为与系统兼容的utf-8
set fileformat=dos  "换行符以\r\n为准
set fileencoding=utf-8  "与系统兼容
syntax enable
```

## Markdown Support

Vim plugins can satisfy almost any need you have. However, here I do not use a Vim plugin; instead, I use a Chrome extension to satisfy my needs. The reason is that the configuration is simpler, and other editors can also use it in the future.

[Plugin installation and usage instructions](https://github.com/volca/markdown-preview)

**Bind a shortcut.** Add this to `vimrc`:

``` vim
" Open markdown files with Chrome.
autocmd BufEnter *.md exe 'noremap <F5> :!start C:\Program Files (x86)\Google\Chrome\Application\chrome.exe %:p<CR>'
```

Press `F5` when using it.

# Afterword

This post was completed with `gvim for MS-WINDOWS`.
