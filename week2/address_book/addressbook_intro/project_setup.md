Okay, enough talking, let's build something. Start by creating a folder in which we'll keep the code base for this entire application, and initialize an empty git repository inside of it.

```bash
$ mkdir address-book
$ git init
$ echo "# Address Book [es2015]" >> README.md
$ git add .
$ git commit -m 'Initial commit, create README.md'
```

A quick note on version control before we continue. Throughout this guide, before we start working on an important piece of functionality for this application, we will create a new branch from `master` and will work from that "_feature_" branch. Once we complete the feature and are happy with the changes, we will merge those changes back to `master` and repeat this process for the next feature.

With that in mind, before we start setting up anything, let's create a new branch in our git repository

```bash
$ git checkout -b 01-project-setup
```

Next, we're going to initialize a javascript project using our package manager. I will be using `yarn` throughout this guide. 


> To install `yarn,` follow the instruction for your operating system on this page [https://yarnpkg.com/en/docs/install](https://yarnpkg.com/en/docs/install)

Once you have yarn installed, run the following command in your terminal to initialize the project.

```bash
$ yarn init
```

You'll be asked a series of questions about your project. It's not super relevant at this stage but answer what you can and skip the rest.

After you've responded to all questions, it will create a `package.json` file which will be used to manage all our project's dependencies. If you open it in your text editor, its content will be similar to the following snippet.

```javascript
{
  "name": "address-book",
  "version": "1.0.0",
  "description": "Address book application",
  "main": "src/app.js",
  "author": "Your Name <email@tld.com>",
}
```

The first thing we will set up for this project is our testing environment using [cucumber-js](https://github.com/cucumber/cucumber-js).


### Cucumber-js

Cucumber is a tool used for testing software. It runs automated acceptance tests written in a behavior-driven development \(BDD\) style. At its core, Cucumber BDD approach is its plain language parser called [Gherkin](https://github.com/cucumber/cucumber/wiki/Gherkin). It is a Domain Specific Language \(DSL\) that allows expected software behaviors to be specified in a logical language that customers can understand. This allows Cucumber allows to execute feature documentation written in business-facing text.

Let's add Cucumber-js and some dependencies to our project.

```bash
$ yarn add cucumber chai puppeteer superstatic --dev
```
make sure that you also add `node_modules` to `.gitignore`, if you don't have `.gitignore` already then go ahead and create it.
```
$ touch .gitignore
```

[Chai](http://www.chaijs.com/) is an assertion library for Node. It provides developers with a number of interfaces to express assertions in tests. We will be using the `expect` syntax of chai to create assertions in our cucumber step definitions

[Puppeteer](https://github.com/GoogleChrome/puppeteer) is a Node library which provides a high-level API to control headless Chrome or Chromium over the DevTools Protocol. Headless browsers are executed in memory and are mostly used in automated test environments where we need to interact with the browser. You can also configure puppeteer to use full \(non-headless\) chrome browser.

[Superstatic](https://github.com/firebase/superstatic) is a static web server. We will use that to serve our application during the development.

Installing packages with `yarn` using the `--dev` option will save the packages as `devDependencies`. After executing the above command your `package.json` file will look as follow:


```javascript
{
  "name": "address-book",
  "version": "1.0.0",
  "description": "Address book application",
  "main": "index.js",
  "author": "John Doe <john@acmecorp.tld>",
  "license": "MIT",
  "devDependencies": {
    "cucumber": "^4.2.1",
    "mocha": "^5.0.5",
    "puppeteer": "^1.2.0",
    "superstatic": "^5.0.1"
  }
}
```

We can now create the various directories in which our cucumber tests and configuration files will reside. 

```bash
$ mkdir -p features/step_definitions
$ mkdir -p features/support
$ touch features/support/world.js
$ touch features/main.feature
```

Every cucumber tests are written in `.feature` files. Add the following basic feature definition that we will use to finalize the basic configuration of cucumber-js

```text
Feature: Create contacts
  As a user
  In order to stay in touch with my friends
  I would like to be able to create a contact for them in my address book


  Scenario: Create a new contact
    Given I visit the site
    Then I should see "Contacts"
    And I should see "You have no contacts in your address book"
```

Next, we'll create an npm script to run cucumber. In your `package.json` file add a `scripts` block with the following content.

```javascript
  "scripts": {
    "cucumber":
      "superstatic -p 3000 2> /dev/null & cucumber-js; lsof -ti tcp:3000 | xargs kill"
  }
```

Let's talk a bit about what's going on with that script

```bash
superstatic -p 3000 2> /dev/null & cucumber-js; lsof -ti tcp:3000 | xargs kill
```

This will do 3 things when executed:

1. `superstatic -p 3000` starts a web server on port `3000` from the current directory using `superstatic`. Our application will then be available on `http://localhost:3000`  `2> /dev/null &` will silence all logs from the superstatic process and push that to the background to allow the next command to be executed in the current terminal session.
2. `cucumber-js` will execute the tests from all `.feature` files in our features directory
3. `lsof -ti tcp:3000 | xargs kill` This last command will kill the web server we started earlier on once the tests are done.

Your `package.json` should now look something like this:

```javascript
{
  "name": "address-book",
  "version": "1.0.0",
  "description": "Address book application",
  "main": "index.js",
  "author": "John Doe <john@acmecorp.tld>",
  "license": "MIT",
  "devDependencies": {
    "cucumber": "^4.2.1",
    "mocha": "^5.0.5",
    "puppeteer": "^1.2.0",
    "superstatic": "^5.0.1"
  },
  "scripts": {
    "cucumber":
      "superstatic -p 3000 2> /dev/null & cucumber-js; lsof -ti tcp:3000 | xargs kill"
  }
}
```

Now that we have the cucumber script in place, let's go ahead and run it.

```bash
$ yarn run cucumber
```

You should get an output similar to the following screenshot on your terminal

![Undefined step-definitions for cucumber](https://raw.githubusercontent.com/CraftAcademy/ca-course-week-2/master/.gitbook/assets/screenshot-2018-04-11-06.58.05.png)

The way cucumber translate the steps written in plain english into actual executable code that tests our application is through what's called step definitions. In the previous output it's telling us that we haven't defined any yet for the steps we have in `main.feature` 

We will copy the suggested steps into a step definition file and run cucumber again. Create a file name `basic-steps.js` inside of the `step-definitions` directory in the `features` directory and paste the following in it.

```javascript
const { Given, Then } = require("cucumber");

Given("I visit the site", function() {
  // Write code here that turns the phrase above into concrete actions
  return "pending";
});

Then("I should see {string}", function(string) {
  // Write code here that turns the phrase above into concrete actions
  return "pending";
});
```

If you run your test again now, you should get the following output

![First step in pending state](https://github.com/CraftAcademy/ca-course-week-2/raw/master/.gitbook/assets/screenshot-2018-04-11-07.06.56.png)

Cucumber now tells us that the step `Given I visit the site` is `Pending`. This is because all our step definition does at this stage is return the word `Pending`. We will return to this later on and write some code to get it to pass.

We have the basic structure in place to run cucumber in our application. We can now move on to the next component, Webpack.


### Webpack

[Webpack](https://webpack.js.org/) is a module bundler. Its main purpose is to bundle JavaScript files for usage in a browser, but you can also use it to transform, build or package any resource or assets in your application. 

We will use it in our application to combine all Javascript files we'll create into `bundle.js` which will be loaded in our `index.html`. Also, because we're using ES6, we need to transpile the code using [babeljs](https://babeljs.io). We will configure webpack to use babel during the build process to transpile our code.

Install the following dependencies in the project

```bash
$ yarn add webpack webpack-cli babel-loader babel-preset-es2015 babel-polyfill babel-register --dev
```

Create webpack config file

```bash
$ touch webpack.config.js
```

And add the following code to it

```javascript
module.exports = {
  entry: "./src/app.js",
  output: {
    filename: "bundle.js",
    path: `${__dirname}/dist`
  },

  module: {
    rules: [
      {
        test: /\.js$/,
        exclude: /node_modules/,
        use: {
          loader: "babel-loader",
          options: {
            presets: ["es2015"]
          }
        }
      }
    ]
  }
};
```

Create a `src` directory and add the `app.js` file in it, which will be the entry point of our application.

```javascript
console.log("Hello April 2018 Cohort!");
```

Create the `index.html` file and add the following snippet in it

```html
<!DOCTYPE html>
<html lang="en">

<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <meta http-equiv="X-UA-Compatible" content="ie=edge">
  <title>Address book</title>
</head>

<body>
  <script src="./dist/bundle.js"></script>
</body>

</html>
```

Note that we're loading `dist/bundle.js` instead of `src/app.js`. `bundle.js` will be the output file after running the build with webpack. Let's create a new script to perform the build. Back in your `package.json` add the following script in the `scripts` block

```javascript
"scripts": {
  "build": "webpack --progress --p --mode=production",
  "watch": "webpack --progress --watch --mode=development",
  "cucumber": "..."
},
```
To generate a build run the following command on your terminal

```bash
$ yarn run build
```

If you open your `index.html` page now in the browser and inspect the page, you should see the text `Hello April 2018 Cohort!` printed in the console.

Every time you make changes to your JavaScript files, you need to run the build command again to generate a new `bundle.js` file. In order to facilitate this process during the development process, you can use the `watch` command instead of `build`. This will generate a build and keep the session open and watch for changes in your current directory and restart the build every time.

Now that everything is setup, we will start building some functionality into this application in the next section.
