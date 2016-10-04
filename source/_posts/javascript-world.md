title: Thoughts on today's javascript world
date: 2016-10-03 22:43:17
tags: [Front-End, javascript]
---
Two years after becoming a Front-End developer, and spending some of my free times learning node.js and some javasciript packages, all I can say to javascript is:

## There are too many f\*\*king thing you need to learn

Yeah, HTML/CSS/javascript is just the very basic, but those could already be the pain for some people. Nowadays nobody works on pure javascript, at least we need jQuery. But jQuery is not good when your code base starts to grow. So there is endless frameworks for webpage, such as Ember.js, AngularJS, Vue.js, React....so many different things you can choose/learn.

Node.js and npm is another beast. Node.js gives developers to run javascript in machines directly without browser, and with full system access like python and C++. Hence, a lot of packages for developing Front-End javascript are developed in Node.js and distributed via npm. 
I would suggest new comers to learn these things in the following order:

### 1. HTML/CSS
The most basic stuff for web pages.
### 2. Browser javascript
This is the basic for the rest of the javascript world.
### 3. jQuery
Pure javascript can be tricky sometimes, and browser compatibility is a nightmare. [jQuery](https://jquery.com/) solves most. It has been around since 2006 so there are tones of jQUery plugins out there. Hence today it is still difficult to not use jQuery completely.
You should keep the use of jQuery in the minimal way. DOM manipulation is very convenient in jQuery, but it is very easy to be abused and create codes that is very difficult to trace and mantain, so use it wisely.
Personally, for new projects I try to use jQuery only for AJAX calls, and maybe some jQuery plugins that I cannot find any good alternative.
### 4. lodash
[lodash](https://lodash.com/) is a collection of utilities like sorting, map array to another array based on a function (map), or transform a list of objects to a object map (keyBy). Another similar package is [underscore.js](http://underscorejs.org/) I found these packages are extremely helpful in my project, so I would definitely recommend you to learn them. Note that you only need one of them in your project.
### 5. Node.js and npm
[Node.js](https://nodejs.org/) is basically javascript for Back-End, but it is not just designed for web development, you can create regular programs too. [npm](https://www.npmjs.com) is the package manager for Node.js. Even if you only want to be a Front-End developer, you should at least learn how to run node.js codes and how to use npm, so that you can install dependencies for your Front-End project.

But I would really recommend learn to code Node.js programs. It's still javascript, and this is the shortcut for you to jump into Back-End development. Or even better, there is [Electron](http://electron.atom.io/), where you can write desktop applications in javascript. There is [NativeScript](https://www.nativescript.org/), where you can write native Android/iOS apps in javascript.
### 6. Whatever you need for your project
At this point you should have the ability to learn most of the packages/frameworks in javascript world.
Keep in mind that since there are so many packages/frameworks out there today, it is impossible to learn them all. So when you see a new framework, only focus on "What" it does, "What" are the features, "What" are the problems it trying to solve. If you think your project can benefit from that package, only then you start to learn "How" to use it. This approach will save your time and prevent you from being overwhelmed by endless new stuffs.

I have been trying to figure out what is my favorite programming language since I graduated from college. I would say for now I love javascript the most The reason? Because you can just learn one language to develop everything for nearly every modern platform.
