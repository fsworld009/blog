title: Semantic UI
date: 2018-01-03 22:37:09
tags: [semantic-ui, javascript, css, Front-End, npm]
---
[Semantic UI](https://semantic-ui.com/) is a component-based webpage layout Framework. I have used it on some of my personal projects and I really like it. Here are some of the benefits:

- Components driven. You only pick up components you need
- Ability to mix different themes in one project
- Provide site level overrides so you don't need to touch theme files themselves
- Built-in Form validation
- Components have initialize and destroy methods so they can be integrated as ReactJs and Vue.js components

<!--more-->
## Dependencies
- jQuery
- npm and gulp for customized builds

## Install
Official Getting started: https://semantic-ui.com/introduction/getting-started.html

You can either download the prepackaged zip or use `npm` and `gulp` to do a customized build. I recommend take some time to understand how to do customized builds, because you cannot change themes or remove unused components in prepackaged version, which is a big selling point for Semantic UI.

1. install `gulp` globally: `npm install -g gulp`
2. install semantic-ui in your project: `npm install semantic-ui`
3. You will see a set-up Semantic UI prompt. I usually go with 'Automatic'->'Yes'->'semantic/' options. You can (and you will have to) edit config files manually later on.
4. After the installation, there will be `semantic.json` and `semantic` folder.

Once you have finished the prompt, it will not be shown again when doing `npm install` again. The solution is to delete semantic-ui folder in `node_modules` and run `npm install` again.

## Basic configurations
In `semantic.json`, you can change the output folders in `output` option. The folders specified in `output` option should be excluded in version control. There is also a `clean` option that should match the output base folder but I found it is not really working.

There is also a `clean` option which you can set clean folder when running `gulp clean`. Unfortunately it will not work if you set it to a folder outside `semantic` folder:

```
Error: Cannot delete files/folders outside the current working directory. Can be overriden with the `force` option.
```
I haven't found a way to solve this issue without changing gulp task source codes.

## Choose themes for components
Official theming guide: https://semantic-ui.com/usage/theming.html

In `src/themes/` You can see all available themes.
To config themes, edit `src/theme.config`. (I recommend to have a backup for this file, like `theme.config.original`). By default all components are using 'default' theme.

The way Semantic UI works is that we configure themes for each components. For example, if I want to use `material` theme for `dropdown` component, I will edit the following line:
```
@dropdown   : 'material';
```

That's pretty much it. However, you need to make sure the theme actually has that component implemented. Say I want to use `material` theme for `form` components, but in `src/themes/material/collections` there is no form components implementations. Hence, if you do
```
@form   : 'material';
```

The output will not have form component at all.

## Edit Site-specific styles
In `src/site`, you can also add specific less variables and styles for the project for each component.

The .variables files are used to override component variables like overriding colors:

src/site/globals/site.variables
```less
/* Material Theme Color rewrite */
/* https://materialuicolors.co/ Level 500 & 200 */
/*---  Colors  ---*/
@blue             : #2196F3;
@green            : #4CAF50;
@grey             : #9E9E9E;
@orange           : #FF9800;
@pink             : #E91E63;
@purple           : #9C27B0;
@red              : #F44336;
@teal             : #009688;
@yellow           : #FFEB3B;

/*---  Light Colors  ---*/
@lightBlue        : #90CAF9;
@lightGreen       : #A5D6A7;
@lightOrange      : #FFCC80;
@lightPink        : #F48FB1;
@lightPurple      : #CE93D8;
@lightRed         : #EF9A9A;
@lightTeal        : #80CBC4;
@lightYellow      : #FFF59D;
```

The .overrides files are used to expend less rules. For example, to have colors in regular texts:

src/site/globals/site.overrides
```less
@text-colors: blue, green, orange, pink, purple, red, teal, yellow, black, grey, white;
.text {
    .-(@i: length(@text-colors)) when (@i > 0) {
        @c: extract(@text-colors, @i);
        &.@{c} { color: @@c }
        .-((@i - 1));
    }.-;
}
```
(source: https://github.com/Semantic-Org/Semantic-UI/issues/1885#issuecomment-226047499)

Both variables and overrides have the same priority setting: `site > chosen theme > default theme`. More details are explained here: http://learnsemantic.com/themes/overview.html


## Build assets
After the configurations above are done, we need to generate final assets so that we can use the framework in our project.

Under `semantic` folder, run `gulp clean build` to generate assets.

Make sure that the script output files in the log if the output folder is outside `semantic` folder and clean task failed, or you can remove the folder manually just to be safe.

Every time you make changes on `semantic.json`, `theme.config`, `site`, you need to rebuild the assets.

Full gulp commands: https://semantic-ui.com/introduction/build-tools.html

## Remove unused components on built assets
Most of the time we are not going to use all components in one project. It is recommend to remove unused components to reduce assets size.


Add `components` property to `semantic.json`:
```json
  "components": [
    "reset",
    "site",
    "button",
    "container",
    "divider",
    "flag",
    "header",
    "icon",
    "image",
    "input",
    "label",
    "list",
    "loader",
    "rail",
    "reveal",
    "segment",
    "step",
    "breadcrumb",
    "form",
    "grid",
    "menu",
    "message",
    "table",
    "ad",
    "card",
    "comment",
    "feed",
    "item",
    "statistic",
    "accordion",
    "checkbox",
    "dimmer",
    "dropdown",
    "embed",
    "modal",
    "nag",
    "popup",
    "progress",
    "rating",
    "search",
    "shape",
    "sidebar",
    "sticky",
    "tab",
    "transition",
    "api",
    "form",
    "state",
    "visibility"
  ]
  ```
then start to remove unused components. Rebuild assets after every edit.

Keep in mind:
1. `site` and `reset` are essential.
2. `transition` and `visibility` are required for some components like `modal`. It is not documented so most of the time I would just leave them included.

Without `components` property, the build script will include all components.

## Documentation and usage notes
- Check [Official website](https://semantic-ui.com/) for documentations.

- Class names may require to be placed in order
    ```html
    <div class="ui grid">
    <div class="four wide column"></div>
    <div class="twelve wide column"></div>
    </div>
    ```
  Put class name as `wide four column` won't work in this case.

- For each component that involves javascript, the component implementations are declared as jquery instance methods, the method name is usually the component name, like `$(selector).dropdown()` is for dropdown and `$(selector),modal()` is for modal.
    - To initialize the component:
```js
$('.ui.dropdown').dropdown(settings)
```
    `settings` is an object that contains initialize options like most jQuery do. Available options are listed under `Settings` tab.

    - To invoke component behavior (methods):
```js
$('.ui.dropdown').dropdown('set value', value);
```
    - Note that behavior name may have spaces and you should not remove them or convert them to camel case.

- Most javascript components offer the `destroy` behavior, we can use it to integrate with React and Vue.js life cycle. Take Vue.js for example, we need to implement `mounted`, `updated`, and `beforeDestroy` life cycle to create and destroy component:

    ```js
    mounted() {
        this.create();
    },
    updated(){
        this.destroy();
        this.create();
    },
    beforeDestroy(){
        this.destroy();
    }, 
    methods: {
        create(){
        var $dropdown = $(this.$el).find(".ui.dropdown");
        $dropdown.dropdown({...});
        },
        destroy(){
        var $dropdown = $(this.$el).find(".ui.dropdown");
        $dropdown.dropdown('destroy');
        }
    }
    ```
    - I would suggest to create a `Dropdown` component to abstract the create/destroy logics for better reusability. Same goes for all Semantic UI components.

## Links
- [Glossary](http://learnsemantic.com/developing/glossary.html)
- [Responsive Grid template](https://semantic-ui.com/examples/responsive.html)