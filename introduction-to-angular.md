![](/assets/angular-logo.svg)

## Angular

Angular is a framework for building dynamic web applications - often referred to as a **single-page applications \(SPA\)**. It lets you use HTML as your template language and extend HTML's syntax to express your application's components. Angular is added to an HTML page with a `<script>` tag.

Angular extends HTML attributes with **Directives**, and binds data to HTML with **Expressions**. **Data binding** is an automatic way of updating the view whenever the model changes, as well as updating the model whenever the view changes.

_**The purpose of this chapter is to give you a basic understanding of the framework and provide you with a foundation to move on to more advanced techniques when working with Ionic.**_** **

### Learning objectives

* Understand basic Angular components such as Modules, Components, Directives, Expressions, Services and Pipes
* Understand the design of single-page applications using Angular
* Build Angular forms and bind data to objects

You are challenged with implementing the code examples as you move forward, commit after every section and push your code to GitHub. As you know, reading about code will not make you a good programmer. Take the opportunity to write as much code as possible.

## Modules

Every angular application must have at least one module, the root module, mostly called `AppModule`. In most of our applications this is possibly the only module you will ever use. This file can be found at `src/app/app.component.ts` and it looks like this:

```typescript
import { BrowserModule } from '@angular/platform-browser';
import { NgModule } from '@angular/core';

import { AppComponent } from './app.component';

@NgModule({
  declarations: [AppComponent],
  imports: [BrowserModule],
  providers: [],
  bootstrap: [AppComponent]
})
export class AppModule {}
```

* **@NgModule:** The tag `@NgModule` is called a decorator which we will discuss in detail later. This decorator takes an object with different properties, such as `declarations`, `imports`, `providers` and `bootstrap`. We will mostly be working  
  with the `providers` and `declarations`.

* **providers:** Services are values, functions, or features that your application needs. We will come back to a more detailed description about services later. All our services are usually registered with the application by adding them to this section. By doing this you make the services available for use in the entire application.

* **declarations:** our components are usually declared in this section. In our case the generated `AppComponent` is declared here.

### Angular modules are not JavaScript modules

OK, as we learnt earlier JavaScript has modules too, but they are different from Angular modules. An Angular module \(NgModule\) is decorated by `@NgModule` and it is completely unrelated to the JavaScript modules.

## Components

Components control a section of the whole page that the user sees, so let's put this into context. Go ahead and have a look at the YouTube screenshot below:

![YouTube sections and components](http://www.rdcs.se/images/youtube-screenshot.png)

From the screenshot above we can see that we can structure the page in different sections:

1. section for playing the video
2. section containing video description
3. section for adding comment and displaying comments
4. section for suggesting other video

Each of the sections is modeled as single components that are stitched together to make up the whole page.

### What components look like

```typescript
import { Component, OnInit } from '@angular/core';

@Component({
  selector: 'app-suggestions',
  templateUrl: './suggestions.component.html',
  styleUrls: ['./suggestions.component.css']
})
export class SuggestionsComponent {

  constructor() { }

  playVideo(video){
    ...
  }

}
```

We start by importing various libraries we need from the Angular core. In this case we have `Component` and `OnInit`.

The `@Component` decorator then tells us that the `SuggestionsComponent` class declared after the decorator \(`@Component`\) is a component. The decorator itself has metadata about the `selector` , `templateUrl`, `styleUrls`, which we will discuss the details later.

Following the decorator we find the `SuggestionsComponent` class, which contains the following  
functions:

* `constructor()`: used for initialization when instantiating an object out of the class, much like ruby class initializers
* `playVideo()`: a function that can be called from the template

### Review the structure of the component

Lets have a look at the component, which was generated for us at  
`src/app/application.component.ts` and identify everything from top to bottom, so that  
you are able to identify each part.  
**IMPORTANT:**  
If you get stuck and unable to understand any of its parts, go back  
and re-read the text above about the component structure.

## Metadata

In TypeScript, in order to attach additional information to a class – for example information about what the class does, where the styling is and what role that class plays in the app – you can use a decorator.

Looking at the code below you can see that we have a decorator in `@Component`. The decorator holds and prefixes a JavaScript object, which in turn carries additional information about the class `SuggestionsComponent` \(the JavaScript object is within `{`and `}`, remember?\).

```typescript
@Component({
  selector: 'app-suggestions',
  templateUrl: './suggestions.component.html',
  styleUrls: ['./suggestions.component.css']
})

export class SuggestionsComponent {
...
}
```

Basically, the decorator is used to give us a brief summary about the class, much like  
the information on hospital wrist bands worn by hospital patients does.

![Hospital wrist band](http://www.globalnerdy.com/wordpress/wp-content/uploads/2016/11/hospital-wristband-2.jpg)

```typescript
@Component({
  selector: 'app-suggestions',
  templateUrl: './suggestions.component.html',
  styleUrls: ['./suggestions.component.css']
})

export class SuggestionsComponent {
...
}
```

In the code above you see that the metadata has three properties:

* `selector`: This is a css selector that tells Angular where to place
  this particular component in the page. We will later use this component in our view by putting `<app-suggestions></app-suggestions>` in the code of the view.
* `templateUrl`: This tells Angular where in our code structure the file containing the view for this component is located. In
  our code above it is located at `./suggestions.component.html`
* `styleUrls`: By now you can guess what this does, which is to tell Angular where our
  stylesheet for the component is located.

### Review the metadata

It's time to make another short review of your understanding. Head over to `src/index.html` and see if you are able to identify how the selector has been used. Grab a coach if you have questions or need confirmation about what you understand.  
**Make sure** you get this right before moving on.

## Templates

The template can be perceived to be the view of the component. By now you should be  
able to tell that a component has its own files for the template, the stylesheet and TypeScript. Templates are much like plain HTML, but with a few extra supermagical abilities and extra tags.

Look closely at the syntax in the code below and you will see a few additions like `*ngFor`, `(click)`, `[video]` and  
`*ngIf`. Don't be nervous over all these new and weird-looking things, we will cover them later.

```html
<h2>Suggested Videos</h2>

<p><i>Click on a video to play</i></p>
<ul>
  <li *ngFor="let video of videos" (click)="selectVideo(video)">
    {{video.name}}
  </li>
</ul>

<video-detail *ngIf="selectedVideo" [video]="selectedVideo"></video-detail>
```

### Review the template

It is now time to review the template, so head over to `application.component.html` and have a look  
at the syntax there. Remove all the unordered list items, as well as the image, and save the changes to see how the changes take effect in the browser.

## The relationship between the template and the component

* the templates are usually tied to a particular component
* the component identifies their respective template through the decorator
* data can flow from the component to the template, from the template to
  the component or both ways

![Diagram of template and component relationship](http://www.rdcs.se/images/diagram-of-template-n-compontent.png)

### Review how to bring components and templates together

Let's generate a component called `comment`:

```shell
$ ng generate component comment
# or a shorter version
$ ng g c comment
```

From the files we have just generated, let's identify what we have learnt so far:

* where in the file structure is the component file located?
* what is the decorator, and what information does the decorator have?
* where is the template file located?

Now add a paragraph with your name in the template, to answer the following questions:

* how do we add our component to a view?
* can we add our component to the index file?
* is it possible to add the component to the `app.component.html` file?

## Summarizing templates and components

**&gt;&gt;TODO: introduction to explanation&lt;&lt;**

![template component relationship summary](https://angular.io/generated/images/guide/architecture/component-tree.png)

**&gt;&gt;TODO: add explanation&lt;&lt;**

**&gt;&gt;TODO: connect above explanation with reflective review using the screenshot below&lt;&lt;**

![YouTube sections and components with children](http://www.rdcs.se/images/youtube-screenshot-children3.png)

# Data binding &gt;&gt; – should this header be h1?&lt;&lt;

Remember when we said that data is able to flow from the template to the component  
and vice versa? Let's talk about how to do this by using data binding.

## Data flowing from the template to the component

In the YouTube example above, when a user clicks on a particular video suggestion the template passes information to the component related to that suggestion telling it that the suggestion was clicked. It also passes along any other information about that particular suggestion that may be embedded in the template. The user's actions on the page are interpreted as events by functions inside the component that handle events \(event handler functions\). These interpreted user events are used to trigger or perform certain functionality or commands within the app. That is how data flows from the template to the component.

In the code below the `handleClick()` function handles the user's `click` event, so the `handleClick()` function is found in the component related to that template. What other events can you think of when interacting with a web page or app?

### Try it out

Add the following to our template `application.compontent.html`

```html
<button (click)="handleClick()">Console log me</button>
```

Hint: **&gt;&gt;&gt;&gt;what does "this" refer to here&lt;&lt;&lt;&lt;** this usually uses `()` eg `(click)="doSomething()"` where `doSomething()`  
is a function in the matching component.

Add the following code to our matching component inside our class. You should be able to match  
components to templates at this point.

```typescript
@Component({
...
})

export class AppComponent {
  title = 'app';

  handleClick(){
    console.log('Someone clicked on button on the template :-)')
  }
}
```

Let's see the code in action in the browser: open the app and the developer tools to see the results.  
What do you see? Click on the button, is anything printed out to the console \(in developer tools\)?

## Reversed data flow from the component to the template

Data is also able to flow in the opposite direction, from inside the app all the way back to the template. In  
this case we have some information in the component that we want to display  
in the template.  
Let's go ahead and create a video in our component!

```typescript
@Component({
...
})

export class AppComponent {
  title = 'app';
  video = {
    title: 'Despacito',
    views: 2,
    liked: true
  }

  handleClick(){
    console.log('Someone clicked the 'Like' button in the template :-)')
}
```

**&gt;&gt;&gt;&gt; NB SIGU: It is now a 'Like' button in the example above&lt;&lt;&lt;&lt;**

Now that we have created a video in our component, how do we go about to display the video inside the template?  
In order to achieve this, we create an expression by using double squiggly brackets `{{ }}`!

In our view lets add the code below to the button:

```html
<p>Video title: {{video.title}}</p>
<p>Views: {{video.views}}</p>
<p>Liked: {{video.liked}}</p>
```

This way of writing data within double squiggly brackets is known as string interpolation - does this ring a bell?

## Two-way data binding

In the previous two scenarios data can only flow in one direction at a time.  
Now we are going to see how data can flow from component to template and from template back to  
component.

This is done by using the syntax `[( )]`, also called "_Banana in a box_".

In order to demo this we need to import Angular form module into our  
`app.module.ts` – do you recall how we did our imports?

```typescript
  import { FormsModule } from '@angular/forms';

  ...

  @NgModule({
    imports: [
      ...
      FormsModule
    ],
    ...
  })
```

Now let's add this to our template: **&gt;&gt;&gt; SIGU: Specify which file to modify here &lt;&lt;&lt;**

```html
 <p>Video name: {{video.name}}</p> <!-- keep in mind that '{{ }}' gets data from the compontent -->
 <input [(ngModel)] = "video.name"/>
```

Now if you try to type something \(anything\) into the input field, what happens?

**&gt;&gt;&gt;&gt; NB SIGU: Where does the below image belong to, above or Directives? Also, should we have a review section here&lt;&lt;&lt;&lt;**

![data binding](https://angular.io/generated/images/guide/architecture/databinding.png)

**&gt;&gt;&gt;&gt; NB SIGU: Suspecting the need for an explanation of the image above plus a review here&lt;&lt;&lt;&lt;**

# Directives &gt;&gt; – should this header be h1?&lt;&lt;

Directives usually change the browser ![DOM](https://en.wikipedia.org/wiki/Document_Object_Model) and have the ability to change elements in the current page. **&gt;&gt;&gt;&gt;Usually you can identify directives by ...&lt;&lt;&lt;&lt;** There are some built-in directives that we will be using more frequently, so let's go ahead and have a look at those.

## \*ngFor

`*ngFor` is used for looping over a collection of data.  
In the example below we have a collection of videos in a component and we use `ngFor` to loop over the collection in order to display data about each video.

```html
<ul>
  <li *ngFor="let video of videos">
    Name: {{video.name}}
    Liked: {{video.liked}}
  </li>
</ul>
```

### Try it out

Add the code above to your template. Now we only need a collection of videos in  
in our component for this to work, so let's put some videos in our component.

```typescript
export class SuggestionsComponent {
 videos = [
    {name: 'video one', liked: true},
    {name: 'video two', liked: false}
 ]

  constructor() { }

  ...

  }
```

## \*ngIf

`*ngIf` renders a section of the template but only if a certain condition is valid or true.

```html
<p *ngIf="isShown">This should be hidden or shown</p>
```

We expect the paragraph above to be shown only if the value of `isShown` is true.  
If the value of `isShown` is false the paragraph should not be displayed on the  
page by the template. **&gt;&gt;&gt;&gt; NB SIGU: Is this an ok way to describe it?&lt;&lt;&lt;&lt;**

We define the value of `isShown` inside the component:

```typescript
export class SuggestionsComponent {
 videos = [
    {name: 'video one', liked: true},
    {name: 'video two', liked: false}
 ]

 isShown: boolean = true

  constructor() { }
```

### Try it out

Which of the two videos in the example above will not be shown to the user by the  
template?  
Answer this before moving on and **make sure to try it out**!

## Services

Services are everything that can act as a data source. For now let's simplify the definition of services to 'the section of an app which is able to give us data that we can use'. The source is in most cases an external API.

Services are also able to validate user input as when the user types it in. The example below shows how the user is denied to use a certain username that is already taken \(yes there are typos in the first screen where a 'C' and a  'T' are missing\).

![Username validation through services](http://www.rdcs.se/images/username.gif)

## Dependency injection

We will be using dependency injection to equip our components with the services that they  
need. Let's say that we have a service that returns a collection of videos from  
YouTube. In order to be able to use this service we will have to inject it into our component:

```typescript
constructor(private videoService: VideoService){}

playVideo(){
 this.videoService.play(video)
}
```

The way to inject a dependency is by writing `constructor(private videoService: VideoService){}`. **&gt;&gt;&gt;&gt; NB SIGU: Is it ok to use "The way to inject a dependency" in this text?&lt;&lt;&lt;&lt;** Let's have a look at the different parts of this:

* `VideoService`: is the service that we are instantiating and used with the `videoService` local
  variable. This must be imported first **&gt;&gt;&gt;&gt; NB SIGU: This fuzzy explanation needs to be clarified, especially that last sentence "This must be imported first" needs to be explained \(what you mean by that\)&lt;&lt;&lt;&lt;**
* `private`: shows that whatever we are declaring is private to this class
* `videoService`: the local variable name that use within the class.
  When used this local variable usually preceded by the `this` keyword as we see in the example above: `this.videoService.play(video)`.



