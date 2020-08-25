---
title: TLDR
date: 2020-05-19 21:48:16
tags:
- bash
- terminal
- tldr
---

Other than reading man pages, [`tldr`](https://tldr.sh/) command shows the most common usages of a given terminal command.

```bash
brew install tldr
```

This is an example output for the `hexo` command. (That's how I create my [blog posts.](../../../../2016/01/18/from-wordpress-to-static-site-generator/))

```bash
$> tldr hexo
✔ Page not found. Updating cache...
✔ Creating index...

  hexo

  A fast, simple & powerful blog framework.
  More information: https://hexo.io/.

  - Initialize a website:
    hexo init path/to/directory

  - Create a new article:
    hexo new layout title

  - Generate static files:
    hexo generate

  - Start a local server:
    hexo server

  - Deploy the website:
    hexo deploy

  - Clean the cache file (db.json) and generated files (public/):
    hexo clean
```

[<- Back to all TILs](../til/)
