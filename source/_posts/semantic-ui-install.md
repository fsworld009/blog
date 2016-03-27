title: Install Semantic UI into your project
date: 2016-03-27 16:47:58
tags: [semantic-ui, javascript, css, Front-End, npm]
---

[Official guide](http://semantic-ui.com/introduction/getting-started.html) is pretty clear. I just make some notes here mainly for references later.

1. install gulp
> install gulp -g

2. install semantic-ui as dev-dependencies
> install semantic-ui --save-dev

3. config screen will popup automatically, I would suggest leave all path as default and include all components

4. go to (project_root)/semantic/src/theme.config to configure themes.
    - Note that most themes don't have icons and images. For example, change everything to "material", but leave @icon and @image as "default".

5. go to semantic folder and run
> gulp build

  - output file will be under dist folder

6. copy semantic.css, semantic.js, themes/default/assets to the folder you want to store css, keep the same relative path for semantic.css and assets folder.
    - If you're using icon and images from different themes, copy those assets also

6. you should gitignore the dist folder
