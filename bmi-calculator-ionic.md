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

## Adding a Page

Now that we have cleaned up the code base we are ready to add our own stuff. We'll be making use of two tabs. One About tab that will route us to an "About page", and one "BMI Calculator" tab that will be displaying the view where we will be doing the calculations.

Just like with rails and angular, Ionic provide us with a generator to scaffold the most common components to an app. We will be using that to generate a new page.

```shell
$ ionic g page calculator
```

If you take a look at your `src/pages/` folder, you'll see that a new folder name calculator has been created.

Every time you created a new page, you need to tell the application about it by importing it in `app.module.ts`. Open it and add the following line at the top of it

```typescript
import { CalculatorPage } from '../pages/calculator/calculator'
```

Then add `CalculatorPage` to the `declarations` and `entryComponents` properties. Next, we also import `CalculatorPage` in `src/pages/tabs/tabs.ts` and create a new tab for it.

```typescript
// Add import statement at top of the file
import { CalculatorPage } from '../calculator/calculator'


export class TabsPage {
  calculatorTab = CalculatorPage; // Create a calculator's tab
  aboutTab = AboutPage            // Rename tab2Root to aboutTab
  ...
}
```

Lastly, let's edit the `tabs.html` template file to render the new tab we just created.

```html
<!-- Your tabs.html file content should be updated to the following -->
<ion-tabs>
  <ion-tab [root]="calculatorTab" tabTitle="Calculator" tabIcon="calculator"></ion-tab>
  <ion-tab [root]="aboutTab" tabTitle="About" tabIcon="information-circle"></ion-tab>
</ion-tabs>
```

Now, check your browser to make sure you can see the new tab we just created. If everything looks good, let's commit our changes and in the next section we'll add some functionality to our calculator.

```shell
$ git add .
$ git commit -m 'add calculator tab'
```

## ![](/assets/angular/bmi-add-calculator-tab.png)Adding functionality

Okay, let's move on to implementing the BMI Calculator. Let's create a form on the `calculator.html` template that will allow the user to input his/her weight and height and return the calculated values.

```html
<!-- type the following content inside <ion-content> tag -->
<ion-list>
  <ion-item>
    <ion-label floating>Weight (kg)</ion-label>
    <ion-input type="number" [(ngModel)]="weight"></ion-input>
  </ion-item>

  <ion-item>
    <ion-label floating>Height (cm)</ion-label>
    <ion-input type="number" [(ngModel)]="height"></ion-input>
  </ion-item>
</ion-list>

<div padding>
  <button block ion-button (click)="calculateBMI()">Calculate</button>
</div>
```

Next, in `scr/pages/calculator/calculator.ts` we'll create two variables, `weight` and `height` that we will use to get information from our view. These two variables should be of type `number`. We also need to declare a function `calculateBMI` in which we'll add some logic later on to calculate the BMI of an individual.

```typescript
// src/pages/calculator/calculator.ts
// Edit your file to have the following code

import { Component } from '@angular/core';
import { IonicPage } from 'ionic-angular';

@IonicPage()
@Component({
  selector: 'page-calculator',
  templateUrl: 'calculator.html',
})
export class CalculatorPage {
  height: number;
  weight: number;

  constructor() {
  }

  calculateBMI() {
    // Add logic to calculate BMI here
  }
}
```



