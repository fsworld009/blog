title: Hexo workflow
date: 2015-07-19 17:20:22
tags: Hexo
---
> hexo server

Start local server, it will listen to any local change and do hexo generate automatically behind the scene.
One issue with this automatically update approach is that deleted tags doesn't remove from tag list, but this can be solved
by close server, then doing manual page generation (hexo generate)

> hexo new (post-name)

Create new post

> hexo new page (page-name)

Create a page, url is (blog-index)/(page-name)

> hexo generate
> hexo deploy

Deploy changes to actual server
