React is a popular JavaScript library for building user interfaces. It was created by developers at Facebook and has quickly become very popular among developers. One way to look at React is that it is the view layer for web applications (the V in Model - View - Controller), but it is much more than that. There are several concepts that React introduces that drastically changes the way you approach building web applications and user interfaces.

React allows you to create highly reusable UI components like checkboxes, radio buttons, dropdown lists, list boxes, buttons, toggles, text fields, date fields, etc. Each of these components, or custom HTML tags, can have their own functionality that displays relevant DOM elements. That is pretty convenient and makes web development more efficient and allows for richer user experience. If you want to know more about this library, head over to Facebook’s resources ["Why React?"](https://facebook.github.io/react/docs/why-react.html) and ["Why did we build React?"](https://facebook.github.io/react/blog/2013/06/05/why-react.html) as they thoroughly explain the technical design choices that make React particularly unique.

It is very convenient to start your journey toward becoming a React-developer by using some of the many popular app generators. [Create React App](https://github.com/facebook/create-react-app) is a tool built by developers at Facebook to help you build React applications. It saves you from time-consuming setup and configuration by abstracting a lot of what makes a React application work away from you. However, there are a number of reasons you may want to make your own implementation, or at least have some idea of what it’s doing underneath the hood. In this course, we will do just that.

We will set up our application using three tools - a package manager, a bundler and a compiler.

- A **package manager** lets you install or update third-party packages and dependencies.

- A **bundler** lets you write modular code and bundle it together into small packages to optimize load time.

- A **compiler** lets you write modern JavaScript code that will still work in older browsers.

The three solutions that will make out our toolchain will be: [NPM](https://www.npmjs.com/), [Babel](http://babeljs.io/) and [Webpack](https://webpack.js.org/). We will go over these tools in more detail further down the road, but feel free to do some reading on your own in the meantime.

## The Project

In this course, we will be building a personal portfolio website. If you are taking part in the [Full Stack Web Developer Bootcamp](https://class.craftacademy.co/courses/course-v1:CraftAcademy+CA-CC-01+2018/about) from Craft Academy, this project and the quiz that follows will be part of your overall grade and become part of your projects portfolio.

If you are taking this course as an independent module, your coach will grade your code and quiz submission before issuing a course certificate.

## Practicals

The only prerequisites for following along with this course is that you need to know relatively basic JavaScript (ES6), HTML and CSS and feel comfortable in your terminal as well as in the Developer Tools of your browser. We recommend that you use Chrome, as most of the screenshots we have included in these materials are from that browser.

This is NOT a read-only kind of course. As with everything else related to programming, you need to actually DO all the exercises and write code. **Writing code on your own is the only way you will learn to code.**

Read the text sections and follow along with the pre-recorded coding demos carefully. Each section will have a coach-led session where we will be doing some coding, solving issues and do Q&A's.

The prime objective of this course is to get a basic understanding of the React library and secondly to finish the coding challenge and produce a personal portfolio application.

A word of caution, do not get too caught up in the styling of your portfolio. It's a slippery slope and can lead to many hours of hunting for that pixel-perfect solution. I'm talking from my own experience. I've bitten off more than I could chew on many occasions in my career and if anything, working on CSS has cost me hundreds of hours of my life. I'm not saying that you should ignore the look and feel of your application. Not at all. What I'm saying is that it's a matter of priorities - you are taking this course to learn the basics of React. Not to build a killer-app.

All code that we're going to write will be in a GitHub repo that we created for this course, so you can easily follow along. Please check the course notes for a link.

## The setup

Start by creating a new folder for your React application. Then, initialize your project with `npm init` (if you want to skip all the questions that follow when you issue that command, add the `-y` flag before you execute it) and open the project in an editor of your choice. I will be using [VSCode](https://code.visualstudio.com/) throughout this course.

Now is also a good time to initiate your git repository and make your first commit. Remember to commit often and use version control functionality if you get stuck or make some mistakes along the way (i.e. feature branches and the `git reset --hard <commit hash>` command).

When our application will build, the source code will be placed in a specific folder (`dist`). Also, we will be installing a lot of external libraries that will reside in the `node_modules` folder. We want to exclude those folders from commits, so let’s go ahead and add a `.gitignore` file that will exclude those directories from version control.

### Execution

Now that you know what we want to achieve, let's run the following commands in your terminal:

```
$ mkdir react_portfolio
$ cd react_portfolio
$ npm init -y
// or
$ yarn init -y
$ git init
$ touch .gitignore
$ echo -e "node_modules/\n.DS_Store\n.vscode\n" >> .gitignore
$ git add .
$ git commit -am "initiates project and adds gitignore scaffold"
```

As a sanity check, you can run the `git log` command to see your current commit history. It's a good habit to check history from time to time. Especially when you collaborate with others. By passing in the `--oneline` flag, you'll get a more readable output. At this point, you should see something like this:

```
$ git log --oneline
15cf366 (HEAD -> master) initiates the project and adds gitignore scaffold
```

## The starting point

In this exercise, we will build your React application and transpile it to executable code that will run on all browsers. Transpilers, or source-to-source compilers, are tools that read source code written in one programming language and produce the equivalent code in another language. Babel is a tool that helps us transpile JavaScript files from latest versions of JavaScript (ECMAScript) to the previous version so that it maintains compatibility against older browser versions. A bit simplified, you might say that everything you do in React must be translated to "vanilla" Javascript - and that Babel does that for us (more on Babel later)

We will be creating most of the files in our `src` folder, and since React is using ES6 features, let the before-mentioned tools (Webpack and Babel) compile it for us into the `dist` folder.

Create the following folder structure for your project:

```
react_portfolio
+-- dist
+-- src
```

We also want to create a starting point for our application. We need an `index.html` file (in the project's root folder) that will be loaded in the browser and hold our React application.

You can use the following scaffold:

```html
<!-- index.html -->

<!DOCTYPE html>
<html>
  <head>
    <meta charset="UTF-8" />
    <meta
      name="viewport"
      content="width=device-width, initial-scale=1, shrink-to-fit=no"
    />
    <meta
      http-equiv="Content-Security-Policy"
      content="default-src *; connect-src * ws://* wss://*; style-src * 'unsafe-inline' 'unsafe-eval'; media-src * ; img-src * data:; font-src * ; script-src * 'unsafe-inline' 'unsafe-eval';"
    />

    <title>Portfolio</title>
  </head>

  <body>
    <div id="app"></div>
    <noscript>
      You need to enable JavaScript to run this React application.
    </noscript>
    <script src="dist/bundle.js"></script>
  </body>
</html>
```

The most important part of the code above is the `<div id="app"></div>` tag, which is the root our React application will hook into, but also the `script` reference to `dist/bundle.js` that will hold our compiled code. The rest is pretty much a standard HTML skeleton.

### Execution

```
$ mkdir src
$ mkdir dist
$ touch index.html
$ code index.html
// add the html code to index.html
```

## React as a dependency

To work with React, we need to install it. As simple as that. React can be added to your project using `npm` or `yarn`(it's your choice, but be consistent.).

## Execution

```
$ npm i -S react react-dom
// or
$ yarn add react react-dom
```

_Please note that `npm i -S` is the equivalent to `npm install --save`._

## Babel and Webpack

As mentioned above, we need to make sure the code we write can be transpiled from ES6 to ES5, as not all browsers support ES6 yet.

### What is ES6?

ES6 (ECMAScript) is a new version of JavaScript that adds some nice syntactical and functional additions. It was finalized in 2015\. ES6 is almost fully supported by all major browsers. But it will be some time until older versions of web browsers are phased out of use. For instance, Internet Explorer 11 does not support ES6, but has about 12% of the browser market share.

### What is JSX?

JavaScript eXtension, or more commonly JSX, is a syntax extension to JavaScript that allows us to write JavaScript code that looks like HTML. With JSX you can write HTML code in JavaScript and have Babel transform these expressions into actual JavaScript code.

More specifically, JSX is a shorthand for calling `React.createElement` function. Since it’s JavaScript, we can execute JavaScript code inside JSX using `{}` curly-brace syntax. More about that later.

All in all, we need to install Babel. Let's break it down a bit more.

`@babel-core` is the main Babel package used to do any transformations on our code. `@babel-cli` lets you compile files from the command line. The other two packages,`@preset-react` and `@preset-env`, are presets that transform specific flavors of code. The `@preset-env` preset transpiles ES6 into more traditional JavaScript (ES5) and the `@preset-react` preset does the same with JSX.

We need to tell Babel that we want to use those presets. In the project's root folder, create a file called `.babelrc` and add the following setting in Json:

```json
// .babelrc

{
  "presets": ["@babel/env", "@babel/preset-react"],
  "plugins": ["@babel/plugin-proposal-class-properties"]
}
```

### Execution

```
$ npm i -D @babel/core @babel/cli @babel/preset-env @babel/preset-react @babel/plugin-proposal-class-properties uglifyjs-webpack-plugin
// or
$ yarn add -D @babel/core @babel/cli @babel/preset-env @babel/preset-react @babel/plugin-proposal-class-properties uglifyjs-webpack-plugin
$ touch .babelrc
// add the json code to .babelrc
```

_Please note that you might get Security Warnings/Alerts if you push to GitHub. Don't stress and follow the instructions. At the time we wrote this walkthrough, the fix was to install the following packages:_

```
$ yarn add -D eslint@^4.18.2 js-yaml@^3.13.1 webpack-dev-server@^3.1.11
```

_Please note that `npm i -D` is the equivalent to `npm install --save-dev`._

The next step is to install and configure Webpack.

We need to install `webpack` as a development dependency and `webpack-cli` so that you can use webpack in the command line.

Webpack uses loaders to process different types of files for bundling. It also works easily alongside the development server that we’re going to use to serve our React project in development and reload browser pages on (saved) changes to our React components.

To get all of this up and running, we need to configure Webpack to use our loaders and prepare the development server (`webpack-dev-server`). The following is a working setup for the `webpack.config.js` - a file you need to create in the project's root folder.

```js
// webpack.config.js

const path = require("path");
const webpack = require("webpack");
const UglifyJsPlugin = require("uglifyjs-webpack-plugin");

module.exports = {
  entry: "./src/index.js",
  mode: "development",
  module: {
    rules: [
      {
        test: /\.(js|jsx)$/,
        exclude: /(node_modules)/,
        loader: "babel-loader",
        options: { presets: ["@babel/env"] }
      },
      {
        test: /\.css$/,
        use: ["style-loader", "css-loader"]
      }
    ]
  },
  resolve: { extensions: ["*", ".js", ".jsx"] },
  output: {
    path: path.resolve(__dirname, "dist/"),
    publicPath: "/dist/",
    filename: "bundle.js"
  },
  devServer: {
    contentBase: path.join(__dirname, "/"),
    port: 3000,
    publicPath: "http://localhost:3000/dist/",
    watchContentBase: true,
    historyApiFallback: true
  },
  optimization: {
    minimizer: [
      new UglifyJsPlugin({
        uglifyOptions: {
          mangle: {
            keep_fnames: true
          }
        }
      })
    ]
  },
  plugins: [new webpack.HotModuleReplacementPlugin()]
};
```

There are many parts here that call for a closer look. Do not take this lightly.

- `entry` tells Webpack where our application starts and where to bundle our files.

- `mode` lets Webpack know we’re working in development mode and saves us from having to add a mode flag when we run the development server.

- `module` is an object that defines how our exported javascript modules are transformed and which ones are included according to the given array of `rules`.

  - The first rule is about transforming ES6 and JSX syntax. The `test` and `exclude` properties are conditions to match files against. We will match anything that is NOT inside the `node_modules` folder. We also need to direct Webpack to use Babel.

  - The second rule configures how we will process CSS. At this point, we will not pre- or post-process our CSS. Meaning we just need to make sure to add `style-loader` and `css-loader` to the `use` property. Later on, we will take a closer look at SASS and will have to change these settings.

- `resolve` allows us to specify which extensions Webpack will resolve — this allows us to import modules without needing to add their extensions.

- `output` property tells Webpack where to put our transpiled and bundled code. The `publicPath` property specifies what directory the bundle should go in, and tells `webpack-dev-server` were to serve files from. If `publicPath` is set incorrectly, we would be getting 404 errors when running the app, since the server would not be serving your files from the correct folder.

- `devServer` property configures the `webpack-dev-server` and specifies the location we will be used to serve static files from as well as the port we want to run the server on.

We also need to add a script to `package.json` to start the `webpack-dev-server`:

```json
// package.json

"scripts": {
    "start": "webpack-dev-server --inline --mode development --open"
}
```

## Execution

```
$ npm i -D webpack@4.19.1 webpack-cli@3.1.1 webpack-dev-server@3.1.8 style-loader@0.23.0 css-loader@1.0.0 babel-loader@8.0.2
$ touch webpack.config.js
// add the javascript code to webpack.config.js
// add the json code to package.json
```

## Our first component - Hello World

Let's create an `index.js` file in our `src` directory, build our first component and hook it in into the DOM.

```js
// src/index.js

import React from "react";
import ReactDOM from "react-dom";

const HelloWorld = () => {
  return <h2>Hello World</h2>;
};

ReactDOM.render(<HelloWorld />, document.getElementById("app"));
```

## Execution

```
$ touch src/index.js
// add the javascript code to src/index.js
```

## See it come alive!

We can start our dev server by executing the `start` script in our terminal, wait until Webpack and Babel does their job, and see our application come alive in the browser:

```
$ npm start
```

![](https://github.com/CraftAcademyLabs/ca_course/raw/master/week3/portfolio_challenge/assets/react_portfolio_1_hello_world.png)

This is probably by far the most complicated "Hello World" setup you've ever done. At least so far into your programming journey. Well, if a "Hello World" application would have been our end-game, then all of the above has been a waste of time. What we've done so far, is to lay out the groundwork for our React application that we are going to build. Our hard work has not been in vain. Also, there are tons of more to React than this. Trust me.

## No quite there yet...

We are not quite done with our Hello World yet. I know, how long can we push this before we get into "real" development? Well, I would like us to do a little bit of refactoring so that we can showcase at least a bit of functionality that showcases the power of React.

Let's extract the Hello tag into a reusable component.

_Note: Up until now we've added the "Execution" section with commands you need to run, to each and every part of the walkthrough. Moving forward, the basic commands like creating files and what content to put in them will only be referenced in the text section. Make sure you read everything and follow along in the prerecorded coding demos carefully._

Let's create a new file in the `src` folder and call it `Hello.jsx`. I that file we will create out `<Hello />` component and reference it from `index.js`. This is the code we need to write:

```js
// src/Hello.jsx

import React from "react";

const Hello = () => {
  return <div> Hello World</div>;
};

export default Hello;
```

Consequently, we need to make some changes to the `index.js`. Read through the following code first, to make sure you understand what's going on, and replace the current content of `index.js`.

```js
// src/index.js

import React from "react";
import ReactDOM from "react-dom";
import Hello from "./Hello";

const App = () => {
  return <Hello />;
};

ReactDOM.render(<App />, document.getElementById("app"));
```

Restart your development server and see if anything has changed.

## Wrap up

At this stage, we have a basic React application configured. It's been a handful, but I hope you have been able to grasp the gist of it. We are using:

- NPM or YARN as a **package manager** that helps us to install or update third-party packages and dependencies.

- Webpack as **bundler** that lets us write modular code and bundle it together into small packages to optimize load time.

- Babel as **compiler** that lets us write ES6 code and make sure our code works in older browsers.

We have made significant progress but have only scratched the surface yet.
