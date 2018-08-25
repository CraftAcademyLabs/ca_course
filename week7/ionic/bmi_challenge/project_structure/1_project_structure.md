Before we start building our application, let's take a close look at the anatomy of an ionic application. Inside the folder that was created during the scaffold, you have a typical Cordova project structure where you'll be able to install native plugins and create platform-specific project files.

### ./src/index.html

`src/index.html` is the main entry point for the app. Much like the index.html file from angular projects you worked on yesterday, you'll spend very little time in this file while building your application. It's main purpose is to setup script and CSS includes and bootstrap, or start running, our application.

For the app to function, Ionic will look for the `<ion-app>` tag in your index.html file. Think of this as the root component for your ionic application.

### ./src/

Inside the src directory, you'll find all your uncompiled code. This is where you'll spend most of your time while building your app. When you run ionic serve, all the code inside of src/ is transpiled into a Javascript version that is supported by most browser.

Find more details about the [project structure here](https://ionicframework.com/docs//intro/tutorial/project-structure/). 

![Project Image](https://raw.githubusercontent.com/magnus-thor/ca_course/cooper_challenge_AUT/images/bmi-code-in-atom.png)
