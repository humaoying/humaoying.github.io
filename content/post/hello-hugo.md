---
title: "Hugo 建站记录"
date: 2021-02-24
draft: false
tags: [Hugo]
categories: [其他]
author: "Farmer"
---

**生命在于折腾，之前使用过 Jekyll、Hexo 静态建站，但效果都不尽如人意，现在尝试使用 Hugo，开启一段新的旅程。**

<!--more-->

本文对于自己建站过程做个简单记录。

# 基础

## 安装 hugo

```bash
brew install hugo
```

## 生成站点

```bash
hugo new site blog
```

## 应用主题 even

进入上面命令生成 blog 文件夹下，执行如下命令：

```bash
git init
git submodule add https://github.com/olOwOlo/hugo-theme-even themes/even
```

将 **theme/even/exampleSite/config.toml** 内容拷贝到 **config.toml**，然后根据个人需求，配置 **config.toml**。

更多使用说明参考 [hugo-theme-even](https://github.com/olOwOlo/hugo-theme-even)

**注意，使用 `git submodule` 拉取和更新执行如下命令：**

```bash
git submodule init
git submodule update
```

## 新建一篇文章

```bash
hugo new post/hello-hugo.md
```

默认生成的文章是草稿，`draft: false`。

## 本地运行

```bash
hugo server
```

如果要将草稿也渲染出来，则执行命令：

```bash
hugo server -D
```

如果一切顺利，那么我们就可以在浏览器看到创建出来的博客页面了。

## 部署到 GitHub Pages

### 部署脚本

参见 [Host on GitHub](https://gohugo.io/hosting-and-deployment/hosting-on-github/)，创建文件 .github/workflows/gh-pages.yml，脚本内容：

```yml
name: github pages

on:
  push:
    branches:
      - master  # Set a branch to deploy

jobs:
  deploy:
    runs-on: ubuntu-18.04
    steps:
      - uses: actions/checkout@v2
        with:
          submodules: true  # Fetch Hugo themes (true OR recursive)
          fetch-depth: 0    # Fetch all history for .GitInfo and .Lastmod

      - name: Setup Hugo
        uses: peaceiris/actions-hugo@v2
        with:
          hugo-version: 'latest'
          # extended: true

      - name: Build
        run: hugo --minify

      - name: Deploy
        uses: peaceiris/actions-gh-pages@v3
        with:
          github_token: ${{ secrets.GITHUB_TOKEN }}
          publish_dir: ./public

```

### 关联 git 仓库

考虑到 `[username].github.io` 可能已经被占用，我们可以新建一个 organization，然后在这个 organization 下面创建一个 `[organization].github.io`，用这个仓库来部署我们的博客。

成功创建仓库后，下面就是水到渠成的常规操作，把本地的 blog 上传到仓库。

# 进阶

## 集成评论

选择 utterances，参考[Hugo 博客使用 utterances 作为评论系统](https://www.midfang.com/hugo-utterances-comment-system/)

## 自定义字体

上面介绍了如何去应用一个主题，那么如何去自定义字体呢？

比如，我的博客使用 [霞鹜文楷](https://github.com/lxgw/LxgwWenKai) 字体，思路如下：

1. 下载需要的字体，放到 `static/fnots` 文件夹 下面
2. 然后在 `assets/sass/_custom` 文件夹下面新建一个 `_custom.scss`，去覆写样式。

**需要注意：不同的主题需要覆写的样式不同，这里只是提供一个思路参考。**

_custom.scss 的内容参考如下：

```scss
// ==============================
// Custom style
// ==============================
// You can override the variables in _variables.scss to customize the style
@font-face {
  font-family: 'LXGW';
  src: url('../fonts/LXGWWenKai-Regular.woff2');
}

@font-face {
  font-family: 'LXGW Mono';
  src: url('../fonts/LXGWWenKaiMono-Regular.woff2');
}

$global-font-family: 'LXGW', 'Source Sans Pro', 'Helvetica Neue', Arial, sans-serif !default;

// Font family of the logo.
$logo-font-family: 'Chancery', cursive, LiSu, sans-serif !default;

// Font family of the code.
$code-font-family: 'LXGW Mono', Consolas, Monaco, Menlo, "DejaVu Sans Mono",
 "Bitstream Vera Sans Mono", "Courier New", monospace !default;

 // Serif font family of the site.
$global-serif-font-family: 'LXGW', Athelas, STHeiti, Microsoft Yahei, serif !default;
```

# 参考

1. [Hugo官网](https://gohugo.io/)
2. [主题even](https://github.com/olOwOlo/hugo-theme-even)
