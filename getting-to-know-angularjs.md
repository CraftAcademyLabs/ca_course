## AngularJS

AngularJS is a framework for building dynamic web applications. It lets you use HTML as your template language and extend HTML's syntax to express your application's components. AngularJS is added to an HTML page with a `<script>` tag.

AngularJS extends HTML attributes with **Directives**, and binds data to HTML with **Expressions**.

What follows is a very basic introduction to AngularJS using common programming techniques. At the end of this chapter we will have a small "Hello ..." application up and running. Initially we will place all our code in one single file (`index.html`). As a last step, we will take a closer look at some best practices and refactor our code. 

**_The purpose of this chapter is to give you a basic understanding of the framework and provide you with a foundation to move on to more advanced techniques when working with Ionic._ **

Let's have a look at a very basic AngularJS application

!FILENAME index.html
```html
<!DOCTYPE html>
<html>
  <head>
    <meta charset="utf-8">
    <script src="http://ajax.googleapis.com/ajax/libs/angularjs/1.6.1/angular.min.js"></script>
  </head>
  <body>
    <div ng-app="" ng-init="message = 'Hello World'">
      <h1>{{message}}</h1>
    </div>
  </body>
</html>

```

![](/assets/angular_hello_world.png)

So what is actually happening here? 

1. Your very basic HTML page is extended with AngularJS **directives** by loading it from a CDN (see explenation below). AngularJS starts automatically when the web page has loaded.
2. The **`ng-app`** directive tells AngularJS that the `<div>` element is the owner of the application.
3. The **`ng-init`** directive initializes AngularJS application variable `message`.
4. AngularJS outputs data stored in the variable `message` where the **expression** is written. In this case `<h1>{{message}}</h1>`.

**That's it. You have a basic AngularJS Hello World application. Let's try something slightly more advanced.** 


<small>A CDN (Content Delivery Network) ​is a collection of global servers that caches and delivers content such as images, videos and Javascript files. By pulling AngularJS from a remote server we avoid having to store it loacally on our computers. This technique is good to use during the development process but advised against for production code.</small> 


### Interacting with a user

Let's see if we can make the AngularJS application to interact with an user action. Closely examine this code, type it in your editor and run it in your browser.

!FILENAME index.html
```html
<!DOCTYPE html>
<html>
  <head>
    <meta charset="utf-8">
    <script src="http://ajax.googleapis.com/ajax/libs/angularjs/1.6.1/angular.min.js"></script>
  </head>
  <body>
    <div ng-app="">
      <input type="text" ng-model="name" placeholder="Give me a name...">
      <h1 ng-if="name">Hello {{name}}!</h1>
    </div>
  </body>
</html>
``` 

Again, let's have a look on what's going on.

1. We are loading AnguarJS from a CDN as we did in the first example.
2. The **ng-model** directive binds the value of the `<input>` control to application data and stores it in the `name` variable.
3. The **ng-if** directive conditions the display of the `<h1>` tag if the `name` variable actually contains any data. 

So when you start typing in the input field, the text "Hello"... is being displayed on the page.

**_Note! If you try to validate this code with https://validator.w3.org/#validate_by_input you'll get some validation errors. 
You can use `data-ng-` instead of `ng-` if you want to make your page HTML valid._**

### Modules and Controllers

So far so good, but there's not much of an application here. Let's introduce modules and controllers and see what they give us.

In AngularJS **modules** define applications while **controllers** control the data and logic. Controllers always belong to a module.

You can add modules and controllers using the `<script>` tag or store them in separate files.

Let's examine some code, write it into our `index.html` and run it in the browser. It's yet another version of the **Hello ...** application but please note that we are moving some of the logic from the html markup (using `ng-` directives) to the controller. 

!FILENAME index.html
```html
<!DOCTYPE html>
<html>
  <head>
    <meta charset="utf-8">
    <script src="http://ajax.googleapis.com/ajax/libs/angularjs/1.6.1/angular.min.js"></script>
  </head>
  <body>
    <div ng-app="demoApp" ng-controller="mainController">
      <h1>{{'Hello', [firstName, lastName].join(' ')}}!</h1>
    </div>

    <script>
      var demoApp = angular.module("demoApp", []);

      demoApp.controller("mainController", function($scope) {
        $scope.firstName = "Thomas";
        $scope.lastName = "Ochman";
      });
    </script>
  </body>
</html>
```

So, let's go over the code.

1. As we already did in the previous examples, we load AngularJS from CDN.
2. We tell the `div` tag that it belongs to the `demoApp` and is controlled by `mainController` (Note that in previous examples we left the `ng-app` directive empty and didn't use the `ng-controller` directive at all) 
3. Within a `<script>` tag, we define the `angular.module` and a `controller` and give them each a name (to be able to reference them). 
4. In the `mainController` we add two attributes to the `$scope` object (`firstName` & `lastName`).
5. We add an expression to the `<h1>` tag where we evaluate some JS code, joining the string "Hello" with the two values we assigned to `$scope` and display **"Hello Thomas Ochman"** on the page (we also add an exclamation mark outside of the expression).

### What is `$scope`? 
Scope (`$scope`) is a JavaScript object that joins the controller with the view and contains the model data. Every controller has an associated `$scope` object.

As an object, `$scope` can have properties and functions.

The scope object is available for both the view and the controller.

In our example we've added 2 properties to `$scope` and we can access them in our view. 

```javascript
demoApp.controller("mainController", function($scope) {
        $scope.firstName = "Thomas";
        $scope.lastName = "Ochman";
      });
```
We only make use of properties here but know that `$scope` can contain objects and functions as well. 

**_There's much more to say about `$scope` but for the sake of this introduction we'll stop here. _**

### Adding Directives

Apart from all the built-in AngularJS directives, you can also create your own. Custom directives can be used for a variety of purposes, here we will demonstrate a way to extract some logic from the view and use a directive instead.


!FILENAME index.html
```html
<!DOCTYPE html>
<html>
  <head>
    <meta charset="utf-8">
    <script src="http://ajax.googleapis.com/ajax/libs/angularjs/1.6.1/angular.min.js"></script>
  </head>
  <body>
    <div ng-app="demoApp" ng-controller="mainController">
      <say-hello></say-hello>
    </div>

    <script>
      var demoApp = angular.module("demoApp", []);

      demoApp.controller("mainController", function($scope) {
        $scope.firstName = "Thomas";
        $scope.lastName = "Ochman";
      });

      demoApp.directive("sayHello", function() {
        return {
            template : "<h1>{{['Hello', firstName, lastName].join(' ')}}!</h1>"
        };
      });
    </script>
  </body>
</html>
```
**_Note that we give the directive a name using `pascalCase` (a variant of `CamelCase`) but use `snake-case` (or rather `lisp-case`) when we write a custom tag to invoke the directive._**
 
Anyway, with this implementation we are on our way to extract the way we construct the template and make it reusable. 

We can do a bit more. How about passing in an attribute to our new directive and add whatever data we pass in to our `$scope`. 

Consider the following implementation for the directive.

```javascript
demoApp.directive("sayHello", function() {
        return {
          scope: false,
          link: function($scope, element, attrs){
            $scope.message = attrs.message;
          },
          template: "<h1> {{[message, firstName, lastName].join(' ')}}!</h1>"
        };
      });
``` 

With this in place, we can use the directive like this: 

!FILENAME index.html
```html
<say-hello message="Hi"></say-hello>
```
And the result should look something like this.

![](/assets/angular_hi_thomas.png)

### Services
In AngularJS **services** are objects or functions that carry out specific tasks. Services can be used as a way to keep and communicate data across the lifetime of the angular app
To use an AngularJS service, you add it as a dependency for the component (controller, directive, etc) that depends on that service.

Let's create a service that returns a collection of users and add it to our implementation.

```javascript
demoApp.service('userService', function(){
   var collection = [
     {firstName: 'Thomas', lastName: 'Ochman'},
     {firstName: 'Amber', lastName: 'Wilkie'},
     {firstName: 'Raoul', lastName: 'Diffouo'}
   ];
   return {
     users: function(){
       return collection;
     }
   }
});
``` 

In order for this service to be available in our controller, we need to add it as a dependency. We also need to call the service in order to store the data it returns in the controller's `$scope` 

```javascript
demoApp.controller("mainController", function($scope, userService) {
  $scope.users = userService.users();
});
```

And finally we want to show the data on our page, right? We will introduce another AngularJS directive in order to do that: **`ng-repeat`**. The **`ng-repeat`** directive repeats a set of HTML, a given number of times (once per item in a collection). You can think of it is a counterpart to Ruby's `each` method.

!FILENAME index.html
```html 
<div ng-repeat="user in users">
  <say-hello message="Hi"></say-hello>
</div>
```

If you update your code with these snippets and run it in your browser, you'll probably notice that you are not getting the desired output. The reason for that is that our directive is not calling the right objects. One way of fixing it is to update the directive with the following (but there are other, better ways to do this, can you figure out how?)

```javascript
demoApp.directive("sayHello", function() {
  return {
    scope: false,
    link: function(scope, element, attrs){
      scope.message = attrs.message;
    },
    template: "<h1> {{[message, user.firstName, user.lastName].join(' ')}}!</h1>"
  };
});
``` 

### Adding functions to a Service
Let's try adding some functionality to our 'Hello...' app. At the moment we are getting a collection of people/users from the `userService`. How about we try to add somebody to that collection? 

Let's start with adding a `<form>` element with 2 `<input>fields to out HTML.

!FILENAME index.html
```html
<form ng-submit="addUser()" >
  <input type="text" ng-model="newUser.firstName" placeholder="Give me a first name...">
  <input type="text" ng-model="newUser.lastName" placeholder="Give me a last name...">
  <button type="submit">ADD</button>
</form> 
```

1. The **`ng-submit`** directive specifies a function to run when the form is submitted.
2. The **`ng-model`** directive binds the value of the `newUser` object (part of `$scope`)

We also need to update our controller by adding the `newUser` object and the `addUser` function.

```javascript
demoApp.controller("mainController", function($scope, userService) {
  $scope.users = userService.users();
  $scope.newUser = {}
  $scope.addUser = function(){
    userService.add($scope.newUser);
  }
});
```

Consequently, we also need to modify our `userService` with an `add` function that will take the object we pass in (`newUser` that now holds the values we added to it) and `push` into the collection of users. As a result the content on our page will be updated with the new data.

```javascript
demoApp.service('userService', function(){
   var collection = [
     {firstName: 'Thomas', lastName: 'Ochman'},
     {firstName: 'Amber', lastName: 'Wilkie'},
     {firstName: 'Raoul', lastName: 'Diffouo'}
   ];
   return {
     users: function(){
       return collection;
     },
     add: function(object){
       collection.push(object);
     }
   }
});
```
![](/assets/angular_new_guy.gif) 

This is the final state of our "Hello ... " application. We have not touched upon how we can store data and a bunch of other things. But as for an introduction, you should be okay for now. 

**We will stop here and move on to Ionic and look at using AngularJS extended with a series of custom directives that makes it possible for us to buildapplications for mobile platforms.** 

### Refactoring
The code we created in this walkthrough is in an desperate need of an overview. This is the time where we look at the implementation and make sure that we follow the conventions and best-practices. A good reference guide is to be found at: https://github.com/johnpapa/angular-styleguide/blob/master/a1/README.md#angular-1-style-guide. Reading through the **"Angular 1 Style Guide"** and looking at our current implementation in the "Hello ... " application we quickly notice that there's a need for some refactoring. 

#### Single Responsibility
The first principle we want to take a look at is about [Single Responsibility](https://github.com/johnpapa/angular-styleguide/blob/master/a1/README.md#single-responsibility) and the **Rule of 1** telling us that we should define one component per file for easier maintenance, testing and readability.

In our case, this folder and file structure should be sufficient. Meaning that we need to create a `js` folder and add 4 files for our components and move each component into the corresponding file.

```
project folder
└─── index.html
└─── js
    └─── app.js
    └─── controllers.js
    └─── directives.js
    └─── services.js
```

#### Definitions
The guide tells us that it is better to use the setter syntax rater than declare modules with a variable.

In our current implementation we store our module in a variable called `demoApp` and chain our components to that variable. 

```javascript
var demoApp = angular.module("demoApp", []);
```
This should be refactored into:

!FILENAME app.js

```javascript
angular.module("demoApp", []);
```

Consequently, when using a module, we will not use a variable but instead use chaining with the **getter** syntax. Our **directive**, for example, will need to be refactored into this: 

```javascript
angular.module("demoApp").directive("sayHello", function sayHello() {
    return {
        scope: false,
        link: function (scope, element, attrs) {
            scope.message = attrs.message;
        },
        template: "<h1> {{[message, user.firstName, user.lastName].join(' ')}}!</h1>"
    };
});
``` 
The same syntax needs to be applied to all components of our "Hello ..." application. 






#### IIFE JavaScript Scopes

The guide tells us that we should wrap all AngularJS components in an Immediately Invoked Function Expression (IIFE). IIFE are be used to avoid variable hoisting from within blocks, protect against polluting the global environment and allow public access to methods while retaining privacy for variables defined within the function. 

In practical terms we need to drop our components into an IIFE that looks like this:

```javascript
(function () {
  // your component
})();
```
As an example, our **`sayHello` directive** should look like this using IIFE:

```javascript
(function () {
    angular.module("demoApp").directive("sayHello", function sayHello() {
        return {
            scope: false,
            link: function (scope, element, attrs) {
                scope.message = attrs.message;
            },
            template: "<h1> {{[message, user.firstName, user.lastName].join(' ')}}!</h1>"
        };
    });
})();
```

#### The `use strict` Directive
Chained method calls on a single line without line breaks are harder to read but we can neaten things up using the **`'use strict';` directive**. This directive defines that JavaScript code should be executed in strict mode and is recommended by the **"Angular 1 Style Guide"**. 

Strict mode makes it easier to write "secure" JavaScript and changes previously accepted "bad syntax" into real errors. As an example, in normal JavaScript, mistyping a variable name creates a new global variable. In strict mode, this will throw an error, making it impossible to accidentally create a global variable. 

In our case, using `'use strict';` makes it safe to write code on new lines and produce more readable code. Our directive can be formatted like this:

```javascript
(function () {
    'use strict';
    angular
        .module("demoApp")
        .directive("sayHello", function sayHello() {
            return {
                scope: false,
                link: function (scope, element, attrs) {
                    scope.message = attrs.message;
                },
                template: "<h1> {{[message, user.firstName, user.lastName].join(' ')}}!</h1>"
            };
        });


})();
```







##### Named vs Anonymous Functions














 







