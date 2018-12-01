## Getting started with React

React is a popular JavaScript library created by developers at Facebook, for building user interfaces. One way to look at React is asthat is the view layer for web applications (the V in Model - View - Controller), but it is much mare than that. It allows you to create highly reusable UI components like tab bars, comment boxes, pop up modals, lists, sortable tables, etc. Each of these componens or custom HTML tags, can have their own functionality that displays relevant DOM elements. That is pretty convinient and makes web development more efficient and allows for richer user experience. If you want to know more about this library, head over to Facebook’s resources ["Why React?"](https://facebook.github.io/react/docs/why-react.html) and ["Why did we build React?"](https://facebook.github.io/react/blog/2013/06/05/why-react.html) as they thoroughly explain the technical design choices that make React particularly unique.

## The Project
We will be building a personal portfolio website. If you are takinkg part in the [Full Stack Web Developer Bootcamp](https://class.craftacademy.co/courses/course-v1:CraftAcademy+CA-CC-01+2018/about) from Craft Academy, this project and the quiz that follows, will be graded and become part of your projects portfolio. 

If you are taking this course as an indipendent module, your coach will grade your submission before issuing a course sertificate. 

## The setup 

It is very convienient to start your journey toward becoming a React-developer by using some of the many popular and really awesome app generators. [Create React App](https://github.com/facebook/create-react-app) is a tool built by developers at Facebook to help you build React applications. It saves you from time-consuming setup and configuration by abstractin a lot of what makes a React app work away from you. However, there are a number of reasons you may want to make your own implementation, or at least have some idea of what it’s doing under the hood. 

One of the main cornerstones of the setup we will be doing is [Babel](http://babeljs.io/) and [Webpack](https://webpack.js.org/). We will go over these tools in more detail further down the road, but feel free to do some reading on your own in the meantime. 

Start by creating a new folder for your React application. Then, initialize your project with `npm init` (if you want to skip all the questions, add the `-y` flag to the command) and open it in an editor of your choice. I will be using [VSCode](https://code.visualstudio.com/) throughout this course.

Now is also a good time to initiate your git repository and make your fist commit. Remember to commit often and use version controll functionality if you get stuck or make some mistakes along the wat (i.e. feature branches and the `git reset --hard <commit hash>` command). 

When our applications will build, the source code will be placed in a specific folder (`dist`). Also, we will be installing a lot of external libraries that will reside in the `node_modules` folder. 
We want to exclude those folders from commits, so let’s go ahead and add a `.gitignore` file that will exclude those directories from version control.

### Execution

Now that you know what we want to achieve, let's run the following commands in your terminal:

```bash
$ mkdir react_portfolio
$ cd react_portfolio
$ npm init -y
$ git init
$ touch .gitignore
$ echo -e "node_modules/\n.DS_Store\n.vscode\ndist" >> .gitignore
$ git add .
$ git commit -am "initiates project and adds gitignore scaffold"
```
## Adding dependencies

We will build our React application and transpile it to executable code that will run on all browser. We will be creating most of the files in our `src` folder, and let generate 
Create the following folder structure for your project:

```
react_portfolio
+-- dist
+-- src
```