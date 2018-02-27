---
title: From Wordpress to Static Site Generator
date: 2016-01-18 23:02:11
tags:
  - hexo
  - node.js
  - blog
  - github
  - wordpress
  - static site generator
---

I admit it is difficult as a software engineer to keep-up with the technology development in our era. Everyday you can read about many new software being published, doing amazing things. And people are helping each other without expecting anything back which is awesome. After sharing my thoughts on open source projects, I would like to tell you the story of how I switched my entire blog from wordpress to an open source static site generator.

# Why

First off all, I hated wordpress. I was always looking for a way to switch my blog site to a better technology because wordpress:

* was cumbersome,
* required a server (or a VPS),
* required a database (MySQL),
* required a lot of maintenance (VPS operating system updates, wordpress updates ... etc.),
* was difficult to use with many plug-ins that you need to purchase for easier usage,
* back-up operation was difficult,
* themes had a lot of variarity but they were expensive.

Then I saw a great video tutorial by Jeff Ammons on Pluralsight: [Build a better blog with a static site generator.](https://www.pluralsight.com/courses/static-site-generator-build-better-blog) When I watched a few minutes of this lesson, I decided this is the new library that I have been waiting for to switch my blog. I cannot go through all the details, tips and tricks in this video, but I would like to give a heads-up and note the basics. I suggest you watch the video if you are planning a migration like I did.

# How

Ok, let's start. Here are the basic steps you need to go through:
1- Select your static site generator library,
2- Choose a theme for your blog site,
3- (Optional) Find a way to migrate your previous blog posts to markdown file,
4- Deploy your new blog site,
5- (Optional) Create a CNAME for your personal domain,
6- Enjoy! (Not optional)

To start with, there are many static site generator libraries and you need to get your hands on them to select your static site generator according to your taste. This web site is an awesome source of information for this task: [StaticGen](http://www.staticgen.com). There are many possibilities and you can choose according to your previous experience with any language. I chose [Hexo](https://hexo.io/), because I like Javascript and hexo is easy to use. It does not require me to set every detail of the library.

Then I started to look for a new theme and I am amazed with the available [theme options](https://hexo.io/themes/). I narrowed down my selections to this list beginning from I like the most:

* [Cactus-Dark](https://github.com/probberechts/cactus-dark)
  * 🌵 A responsive, dark and simple theme for Hexo.
* [Tranquilpeak](https://github.com/LouisBarranqueiro/hexo-theme-tranquilpeak)
  * A gorgeous responsive theme for Hexo. - [Demo](http://louisbarranqueiro.github.io/hexo-theme-tranquilpeak/)
* [Phase](https://github.com/hexojs/hexo-theme-phase)
  * The most beautiful theme for Hexo. - [Demo](https://hexo.io/hexo-theme-phase/)
* [TKL](https://github.com/SuperKieran/TKL)
  * A responsive design theme for Hexo. - [Demo](http://go.kieran.top/post/14/)
* [Alberta](https://github.com/ken8203/hexo-theme-alberta)
  * A simple, textured and responsive theme with your own photo. - [Demo](http://jaychung.tw/)
* [Persona Color](https://github.com/heruoxin/hexo-persona-color)
  * A responsive theme with 4 color schemes. - [Demo](http://1ittlecup.com/)
* [Snow](https://github.com/akar1nchan/hexo-theme-snow)
  * A white theme for Hexo based on Landscape. [Demo](http://akarin.xyz/)

## Selection

[Tranquilpeak 1.4](https://github.com/LouisBarranqueiro/hexo-theme-tranquilpeak) seemed like a new version of [Alberta](https://github.com/ken8203/hexo-theme-alberta) with a lot more capabilities and integrated services. Also it's documentation was really good and its developer [Louis Barranqueiro](https://github.com/LouisBarranqueiro) is really helpful. He answered all my questions in an instant with detailed explanations.

I used [Tranquilpeak](https://github.com/LouisBarranqueiro/hexo-theme-tranquilpeak) for 2 years and as I got more experienced with static site generators, I moved to [Cactus-Dark](https://github.com/probberechts/cactus-dark). I liked the minimalist approach with this theme.

## Migration

There are also plug-ins for Hexo to export your previous blog posts from wordpress to a markdown file. (Markdown file is the source file for your posts in Hexo, see details from [Hexo web site](https://hexo.io/).) So I easily exported my previous posts to my new blog as well. I had to change a few details on every exported post, though it wasn't a hard work.

## Deployment

Last step is deployment and it couldn't be any easier. [Github](https://github.com/) provides a free and easy solution for your personal blog as long as it is static content. You can see the details from [here](https://pages.github.com/). So I created a new public repository with my github name and voila. After the first commit, my new blog site was operational just like that. I also created a CNAME file to direct my personal domain name to this repository as well. See [here.](https://help.github.com/articles/setting-up-a-custom-domain-with-github-pages/)

# Summary

My whole experience summary is as follows:

* Migration process was easy and fun,
* I got to learn a bit of Node.js,
* I do not need to maintain a VPS now, (which my wordpress site used to work on)
* I do not need to pay for a VPS now,
* My blog site is a lot more faster,
* Writing a new post in Hexo is easy.