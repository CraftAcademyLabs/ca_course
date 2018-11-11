# AngularJS


## A basic application
```
$ bower init
```

Install Angular

```
$ bower install angularjs --save
```

Create `index.html` 

```html
<!DOCTYPE html>
<html lang="en-US">
<script src="http://ajax.googleapis.com/ajax/libs/angularjs/1.6.1/angular.min.js"></script>
<body ng-app="">

<div >
 	<p>Name : <input type="text" ng-model="name"></p>
 	<h1>Hello {{name}}</h1>
</div>

</body>
</html>
```
