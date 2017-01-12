### AngularJS

AngularJS is a framework for building dynamic web applications. It lets you use HTML as your template language and extend HTML's syntax to express your application's components. AngularJS is added to an HTML page with a <script> tag.

AngularJS extends HTML attributes with Directives, and binds data to HTML with Expressions.

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

