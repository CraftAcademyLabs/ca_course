### AngularJS

AngularJS is a framework for building dynamic web applications. It lets you use HTML as your template language and extend HTML's syntax to express your application's components. AngularJS is added to an HTML page with a `<script>` tag.

AngularJS extends HTML attributes with **Directives**, and binds data to HTML with **Expressions**.

**_What follows is a very basic introduction to AngularJS. The purpose of this chapter is to give you a basic understanding of the framework and provide you with a foundation to move on to more advanced techniques when working with Ionic._ **

Let's have a look at a very basic AngularJS application

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

1. Your vary basic HTML page is extended with AngularJS **directives** by loading it from a CDN. AngularJS starts automatically when the web page has loaded.
2. The **`ng-app`** directive tells AngularJS that the `<div>` element is the owner of the application.
3. The **`ng-init`** directive initializes AngularJS application variable `message`.
4. AngularJS outputs data stored in the variable `message` where the **expression** is written. In this case `<h1>{{message}}</h1>`.

**That's it. You have a basic AngularJS Hello World application. Let's try something slightly more advanced.** 

### Interacting with a user

Let's see if we can make the AngularJS application to interact with an user action. Closely examine this code, type it in your editor and run it in your browser.
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
There's much more to say about `$scope` but for the sake of this introduction we'll stop here. 

### Adding Directives

Apart from all the built-in AngularJS directives, you can also create your own. Custom directives can be used for a variety of purposes, here we will demonstrate a way to extract some logic from the view and use a directive instead.

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
```html
<say-hello message="Hi"></say-hello>
```
And the result should look something like this.

![](/assets/angular_hi_thomas.png)



 







