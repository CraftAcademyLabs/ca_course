## What is Ionic?
Ionic is a web component library that helps us webapps and mobile application for both ios and android

We can use a tool called Capacitor in order to turn web apps into cross platform apps

Why would we use ionic?
So first of all you don't need to use react, angular or any framwwork with ionic. You could potentially only use vanilla javascript, html and CSS. But in order for us to have a faster development process it is advisable to use a framework. We are going to use react, beacuse it is the one we have most experience with. 

## What is a hybrid app

## Alternetives to Ionic

## Components

## Intro to Ionic and mobile apps

We are going to create a simple hello world application with a tool called Ionic.
Ionic will help us create PWA and mobile applications with the help of the languages JavaScript, HTML and CSS. 

However we would like to use React as a library when working with Ionic as this will make sure that we can create new features, but also to avoid manually having to create boilerplate code, as this is handled for us by React. 

This guide requires you to know React at a basic level. There are a few issues though when working with React and Ionic. The first thing is that Ionic adds Typescript to your react application by default. This is in no way a bad thing. But in our case, we work with vanilla JS. But we do encourage you to look into TypeScript in the future. TypeScript as risen a lot in popularity amongst companies and developers during the resent year. But in this guide to keep this as basic as possible we will only work with React and ES6/ES7 to create our mobile application in Ionic. 

**Let's get started:**

Install Ionic globally. This will install ionic globally and make sure that you can start a project regardless of what folder you are in on the computer. 

    $ npm install ionic@latest -g

And now to create a new project

    $ $ ionic start
    
   This command will start your project and create. new folder that will house your Ionic app. However, you will be prompted to give some informations.  for example:
   App name, framework and template.

For the app name choose "helloUniverse" (A working title), for the framework, chose to react from the list, and last for the template choose the blank template. It will also ask you if you want to add capacitor. Say no for now. We can always add it in later. More about Capacitor in later guides.

You could, of course, do all of this in a single command: 
```bash
$ ionic start helloUniverse blank --type=react
```
But sometimes its better to see all the options we have. 

Before we dive in to the files head over to your terminal and run `$ ionic serve` this will start up your server, and hopefully you will see the message "**Ready to create an app?**"

Go over your code. You will probably recognize a lot from your experience with React.  But there are some differences. 
We have different configuration files and we also different file suffixes than what we are used to mainly `tsx`.
tsx stands for typescript as we mentioned before. And we need to make sure that we rename all the tsx files to jsx files instead. 

But for now rename the `App.tsx` to `App.jsx` and `index.tsx` to `index.js`. So a natural question arises. Will this not break the entire thing? Well not really, and the reason for that is that TypeScript is a typed superset of JavaScript that compiles to plain JavaScript. This means that any code we write in plain TypeScript will in the end be rendered as plan JavaScript.

But there are some changes inside of the App.jsx that we need to make. Previously we had an tsx file so the content reflects that. Lets change it to something more recognizable. 


First of all change the App functional component to the standard ES6 format without TS.

```js
const App = () => {
  return (
    <IonApp>
      <IonReactRouter>
        <IonRouterOutlet>
          <Route path="/home" component={Home} exact={true} />
          <Route exact path="/" render={() => <Redirect to="/home" />} />
        </IonRouterOutlet>
      </IonReactRouter>
    </IonApp>
  )
};
export default App;
```






 
