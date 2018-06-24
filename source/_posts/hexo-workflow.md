title: Hexo workflow
date: 2015-07-19 17:20:22
tags: Hexo
---

### Start local server
```
hexo server
hexo server -p (port)
```
It will listen to any local change and do hexo generate automatically behind the scene.
One issue with this automatically update approach is that deleted tags doesn't remove from tag list, but this can be solved
by close server, then doing manual page generation (hexo generate)

### Create new post
```
hexo new (post-name)
```
### Create new page
```
hexo new page (page-name)
```
url is (blog-index)/(page-name)

### Deploy changes to actual server
```
hexo generate
hexo deploy
```

## Notes
- Built-in tags https://hexo.io/docs/tag-plugins.html

### Deployment
- You can force hexo to regenerate all pages by deleting `public` folder and then do `hexo generate`, useful for issues like title is not updated in article list pages.
- For using git as deployment method, deleting `.deploy_git` folder and then do deployment will result a force update on target server/branch, might be useful if you don't want to preserve older versions of articles and pages.