## Prerequisites

During week 2, you worked on a small web application to practice JavaScript, the BMI Calculator. This week we will build a mobile version of that application. Before we get started on that, let's make sure we have everything we need setup on our computer.

#### Node.js

You need to install Node.js. If you haven't installed this already, please refer to the instructions [available here](https://prepcouse.books.craftacademy.se/terminal/mac_osx.html) to do so. To check your version of node run

```
$ node -v
v8.1.2 # Might be a different version on your system
```

#### Ionic and Cordova

Install the latest `Cordova` and `Ionic` command-line tools.

```
$ npm install -g cordova ionic
```

iOS development requires iOS simulator which you can install with

```
$ npm -g install ios-sim
```

Better yet, you should make sure that you have [Xcode installed](https://developer.apple.com/xcode/download/) on your system.

Good stuff. You are ready to start your project.

## Initiate your app

We will make use of the [Ionic Tabs](https://github.com/driftyco/ionic-starter-tabs) template. Navigate to the folder where you want to create your project and run the following command to start your application.

```
$ ionic start bmi_calculator tabs
```

Once that is complete, run the following commands to add support for iOS and Android.

```
$ cd bmi_calculator
$ ionic platform add ios     # if you're on macOS
$ ionic platform add android # if you need to publish for android.
```

At this point, you usually want to make sure you initialize a git repository inside the project folder. This is such so important that the good folks from ionic team built this in as part of the process of scaffolding the app. If you were paying attention, you might have noticed that after installing all necessary project dependencies, a git repository was initialized and the _Initial commit_ was made. All you have to do now is, create a new repository on GitHub and add it as a remote. _Git flow is outside the scope of this walkthrough but do make use of version control when working on this project._

### Run the application {#run-the-application}

In your terminal run the following command to start a local server and run the application

```
$ ionic serve --lab
```

A browser window will open and you'll be presented with a view of both the iOS and the Android version of the application. **Pretty cool, right?**

![](/assets/Screenshot 2017-08-07 23.31.24.png)

