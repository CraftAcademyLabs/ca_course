## Prerequisites

During week 2, you worked on a small web application to practice JavaScript, the BMI Calculator. This week we will build a mobile version of that application. Before we get started on that, let's make sure we have everything we need setup on our computer.

#### Node.js

You need to install Node.js. If you haven't installed this already, please refer to the instructions [available here](https://prepcouse.books.craftacademy.se/terminal/mac_osx.html) to do so. To check your version of node run

```
$ node -v
v8.1.2 # Might be a different version on your system
```

#### Ionic and Cordova

Install the latest `Cordova` and `Ionic` command-line tools.

```
$ npm install -g cordova ionic
```

iOS development requires iOS simulator which you can install with

```
$ npm -g install ios-sim
```

Better yet, you should make sure that you have [Xcode installed](https://developer.apple.com/xcode/download/) on your system.

Good stuff. You are ready to start your project.

## Initiate your app

We will make use of the [Ionic Tabs](https://github.com/driftyco/ionic-starter-tabs) template. Navigate to the folder where you want to create your project and run the following command to start your application.

```
$ ionic start bmi_calculator tabs
```

Once that is complete, run the following commands to add support for iOS and Android.

```
$ cd bmi_calculator
$ ionic platform add ios     # if you're on macOS
$ ionic platform add android # if you need to publish for android.
```

At this point, you usually want to make sure you initialize a git repository inside the project folder. This is such so important that the good folks from ionic team built this in as part of the process of scaffolding the app. If you were paying attention, you might have noticed that after installing all necessary project dependencies, a git repository was initialized and the _Initial commit_ was made. All you have to do now is, create a new repository on GitHub and add it as a remote. _Git flow is outside the scope of this walkthrough but do make use of version control when working on this project._

### Run the application {#run-the-application}

In your terminal run the following command to start a local server and run the application

```
$ ionic serve --lab
```

A browser window will open and you'll be presented with a view of both the iOS and the Android version of the application. **Pretty cool, right?**

![](/assets/angular/ionic-lab-bmi.png)

## Project Structure

Before we start building our application, let's take a close look at the anatomy of an ionic application. Inside the folder that was created during the scaffold, you have a typical Cordova project structure where you'll be able to install native plugins and create platform-specific project files.

### ./src/index.html

`src/index.html` is the main entry point for the app. Much like the `index.html` file from angular projects you worked on yesterday, you'll spend very little time in this file while building your application. It's main purpose is to setup script and CSS includes and bootstrap, or start running, our application.

For the app to function, Ionic will look for the `<ion-app>` tag in your `index.html` file. Think of this as the root component for your ionic application.

### ./src/

Inside the `src` directory, you'll find all your uncompiled code. This is where you'll spend most of your time while building your app. When you run `ionic serve`, all the code inside of `src/` is transpiled into a Javascript version that is supported by most browser. 

Find more details about the [project structure here.](https://ionicframework.com/docs//intro/tutorial/project-structure/) ![](/assets/angular/bmi-code-in-atom.png "BMI Calculator source code in Atom")

## Cleaning up

We will now remove our home and contact pages, then add a new page called calculator. Before we do that, let's create a new branch called `calculator`

```
$ git checkout -b calculator
```

Now, navigate to `src/pages` and delete `home` and `contact`. Then open `src/app.module.ts` \(in your text editor\) and remote both `HomePage` and `ContactPage` from the `declarations` and `entryComponents` properties. You also want to remove respective `import` statements from this file. 

```typescript
// Remove the following lines
import { ContactPage } from '../pages/contact/contact';
import { HomePage } from '../pages/home/home';
```

Next, open `src/pages/tabs.ts` and remove import statements for `HomePage` and `ContactPage` and remove the following lines too

```typescript
// Remove the following lines
tab1Root = HomePage;
tab3Root = ContactPage;
```

And finally in `src/pages/tabs.html` , we will remove lines that set both `tab1Root` and `tab3Root`

```html
<!-- Remove the following lines -->
<ion-tab [root]="tab1Root" tabTitle="Home" tabIcon="home"></ion-tab>
<ion-tab [root]="tab3Root" tabTitle="Contact" tabIcon="contacts"></ion-tab>
```

At this point, if you take a look at your browser, you should see something similar to![](/assets/angular/bmi-cleaned-app.png)

Next up, let's see how to add new pages to our app. But before we carry on, let's commit all the changes we just made

```shell
$ git add .
$ git commit -m 'remove some of the scaffolded pages'
```



