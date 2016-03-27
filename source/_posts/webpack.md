title: webpack
date: 2016-03-27 14:50:37
tags: [javascript, Front-End, webpack, npm]
---

[webpack](https://webpack.github.io/) is a module bundler for Froned-end web development. It aims to provide a way to automate resources including, minifying, and uglifying so that you don't need to manually insert them in your HTML.

There are other packages doing front-end resource packaging or load handling, such as require.js, bower, browserify. I don't have much experience on these packages so I'm not going to do any compare&comparison here. I chose webpack simply because it looks like the JS community is in favor of webpack at this point.


## Pros and cons
Some advantages I can see after I tried webpack:
- You only need to include the bundle script in the html, no need to keep adding script and link tags
- Be able to use require("") in browser javascript, separating modules and reduce the use of global variables
- You can require packages from node_modules like you do in Node.js code, there are some advantages of doing this:
    - You can share common-used libs (like jQuery, underscore) between Front-end and Back-end code
    - Update Front-end dependencies directly by npm, you don't need to replace resources manually anymore

As of cons, I haven't really do heavy developments so I cannot tell mush, I can only think of two now:
  - It's not easy to make webpack work in the beginning, it took me almost 2 days to bundle a Hello World app with React and Semantic UI.
  - You need run webpack every time you update something in your code, this can be solved by using [webpack-dev-server](https://webpack.github.io/docs/webpack-dev-server.html) package, but again it is another pain to set it up properly. I'll briefly talk about it at the end of the article

I'm not an export on it so in this article I'll only show my working config and some key points.

The webpack version is 1.12.14 by the time I wrote this article.


## Example script
```js
require("./css/semantic.css");

window.jQuery = window.$ = require("jquery");
var semantic = require("./js/semantic.js");
var React = require("react");
var ReactDOM = require("react-dom");

var HelloWorld = React.createClass({
  render: function() {
    return (
      <p>
        Hello, <input type="text" placeholder="Your name here" />!
        It is {this.props.date.toTimeString()}
      </p>
    );
  }
});

setInterval(function() {
  ReactDOM.render(
    <HelloWorld date={new Date()} />,
    document.getElementById('app')
  );
}, 500);

```
Here, I require jQuery and react from node_modules. For jQuery, I need to use window object because of the semantic ui scripts.

## Install
> npm install webpack --save-dev

Make sure you save it as dev dependencies only. I would suggest install it globally also, if this is your first time using it.

> npm install webpack -g

The reason is that you can test it without "npm run" commands. just type 'webpack' in bash and kick of the bundle process.


## Config
Take this config file for example:

```js
var path = require('path');
var webpack = require('webpack');

var config = {
    entry: './web/index.jsx',
    output: {
        path: path.resolve(__dirname, 'web/build/'),
        publicPath: "./build/",
        filename: 'bundle.js'
    },
    module: {
        loaders: [
              {
                test: /\.jsx$/,
                loader: 'babel-loader',
                query: {
                  presets: ['babel-preset-react']
                }
            },
            { test: /\.json$/, loader: "json-loader" },
            { test: /\.css$/, loader: 'style-loader!css-loader' },
            { test: /\.(png|jpg|gif)$/, loader: 'url-loader?limit=8192' }, // inline base64 URLs for <=8k images, direct URLs for the rest
            { test: /\.woff(2)?$/,   loader: "url-loader?prefix=font/&limit=5000" },
            { test: /\.(eot|ttf|svg)$/,    loader: "file-loader?prefix=font/" }
        ]
    }
};
module.exports=config;

```
my project folder structure:
- root
  - web
    - build
      - (bundle.js goes here)
    - index.html
    - index.jsx

## Key points
- By default the config file is at webpack.config.js in your project root.
- **It seems that there is no type checking when paring config files**, so make sure you're passing lists when you should, or else you'll end up getting errors from webpack dependencies and stuck for a few hours [like me](https://github.com/webpack/webpack/issues/2242)
- entry property specifies the main script file to pack, I'm doing a single page app now so there is only one file. For multiple entry points, check [this article](https://webpack.github.io/docs/multiple-entry-points.html)

- publicPath means "the relative path from index.jsx to the output file", it is used when you're bundling any files other than css/js (like fonts and sprites)

- You need to specify loaders to deal with each type of resource files.
I construct the loaders mainly from this [webpack-example repo](https://github.com/webpack/example-app/blob/master/webpack.config.js) and [this howto repo](https://github.com/petehunt/webpack-howto)
- For react JSX files (\*.jsx), you need to use loader "babel-loader" and preset "babel-preset-react" to handle JSX syntax.
- **Make sure you installed all loaders and presets in your config**, for the example above, I need to npm install these packages as dev-dependencies:
    - > npm install babel-loader babel-preset-react style-loader css-loader url-loader file-loader ---save-dev


## Bundle
run
> webpack

or
> webpack --config webpack.dev-pack.config.js

if you want to specify which file you want to use

Now the only script you need to insert into the index.html is the bundle.js files
```html
<script src="./build/bundle.js"></script>
```

## webpack-dev-server
webpack-dev-server gives you a http server that watches changes and do bundle tasks automatically. You should only use it for dev purposes.

Install it from npm:
> npm install webpack-dev-server --save-dev

Or globally
> npm install webpack-dev-server -g

In order to work with webpack-dev-server, I changed my config as folows:
```js
var path = require('path');
var webpack = require('webpack');

var config = {
    entry: ['webpack/hot/dev-server', path.resolve(__dirname, 'web/index.jsx')],
    output: {
        path: path.resolve(__dirname, 'web/build/'),
        publicPath: "/web/build/",
        filename: 'bundle.js'
    },
    module: {
        loaders: [
              {
                test: /\.jsx$/,
                loader: 'babel-loader',
                query: {
                  presets: ['babel-preset-react']
                }
            },
            { test: /\.json$/, loader: "json-loader" },
            { test: /\.css$/, loader: 'style-loader!css-loader' },
            { test: /\.(png|jpg|gif)$/, loader: 'url-loader?limit=8192' }, // inline base64 URLs for <=8k images, direct URLs for the rest
            { test: /\.woff(2)?$/,   loader: "url-loader?prefix=font/&limit=5000" },
            { test: /\.(eot|ttf|svg)$/,    loader: "file-loader?prefix=font/" }
        ]
    },
    plugins: [
      new webpack.NoErrorsPlugin()
    ]
};
module.exports=config;
```
Key Differences:
- add "webpack/hot/dev-server" in entry
- publicPath needs to be "absolute path with your project home as root"
- (optional) add webpack.NoErrorsPlugin() to stop hot reloading when the script has error

Now run dev-server
> webpack-dev-server --config webpack.dev-server.config.js --devtool eval --progress --colors --hot

Then go to http://localhost:8080/webpack-dev-server/web/ to see the page. Make some changes on jsx and save, you should see hot reloading happens. Saving HTML page doesn't trigger a hot reload, I guess it only watches files listed in "entry" property.


check all availables options at [webpack-dev-server](https://webpack.github.io/docs/webpack-dev-server.html#webpack-dev-server-cli)




Finally, if you don't want to install webpack and webpack-dev-server globally, you need to put the config in package.json:

```json
{
  ...
  "scripts": {
    "dev-pack": "webpack --config webpack.dev-pack.config.js",
    "dev-server": "webpack-dev-server --config webpack.dev-server.config.js --devtool eval --progress --colors --hot"
  }
}
```

and do

> num run dev-pack  
> num run dev-server

respectively


## References
http://rhadow.github.io/2015/04/02/webpack-workflow/
http://survivejs.com/webpack_react/developing_with_webpack/
http://www.christianalfoni.com/articles/2015_04_19_The-ultimate-webpack-setup
https://github.com/dmachat/angular-webpack-cookbook/wiki/Deploy-to-production
https://github.com/petehunt/webpack-howto
http://blog.kkbruce.net/2015/10/webpack.html#.Vvfymj-rii4
https://github.com/webpack/example-app/blob/master/webpack.config.js


[Error: Cannot resolve module 'babel-loader'](http://stackoverflow.com/questions/34538466/error-cannot-resolve-module-babel-loader)  
[How to set resolve for babel-loader presets](http://stackoverflow.com/questions/34574403/how-to-set-resolve-for-babel-loader-presets)  
[Module not found: Error: Cannot resolve module" for react-router and react](https://github.com/petehunt/webpack-howto/issues/30)  
[Managing Jquery plugin dependency in webpack](http://stackoverflow.com/questions/28969861/managing-jquery-plugin-dependency-in-webpack)
