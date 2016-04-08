# Saving and retrieving data - step 6
At this stage we have a possibility to interact with the back and login to the mobile application. It is time to set up a mechanism to both save and retrieve our test results.

We have an API endpoint in our beck-end that can take a POST and a GET request, right? Let's build a service that will give us a way to access it. 

We will use [`ngResource`](https://docs.angularjs.org/api/ngResource) to access out API and perform out requests. 

```
$ bower install angular-resource --save
```

Create a new file in the `www/js` folder and call it `services.js` and make sure to reference it in the `index.html` together with all the other dependencies.

```
$ touch www/js/services.js
```
!FILENAME www/index.html
```html
  <script src="lib/angular-resource/angular-resource.js"></script>
  <!-- your app's js -->
  <script src="js/cooper.js"></script>
  <script src="js/app.js"></script>
  <script src="js/controllers.js"></script>
  <script src="js/services.js"></script>
```
Since we have not used any services of factories before in app, we need to add that to our main module in `app.js`. We also need to make sure that `ngResource` is included. 

!FILENAME www/js/app.js
```javascript
angular.module('starter', ['ionic', 'starter.controllers', 'starter.services', 'ng-token-auth', 'ngResource'])
// ...

```

Okay, so now we need to create our Factory.

!FILENAME www/js/services.js

```javascript
angular.module('starter.services', [])

.factory('performaceData', function($resource, API_URL){
  return $resource(API_URL + '/data', {}, {
    all: {
      headers: {HTTP_ACCEPT: 'application/json'}
    }
  });
});
```

With this factory we will be able to both write to and read from our back-end database. 

Let's create a new controller for doing that. Remember that we need to include that factory in our controller in order to make it accessible. We'll also add two methods, one to save the data and a second one to retrieve it. 

!FILENAME
```javascript
.controller('PerformanceCtrl', function($scope, performaceData){
  $scope.saveData = function(){

  };
  $scope.retrieveData = function(){

  };
})

```

Let's start with saving the data. On our view where we do the calculations, we want to display a button that calls the `saveData` function. But we only want to display thet button IF there is a user logged in. 

Modify your `test.html` template with this code. 

!FILENAME www/templates/test/test.html
```html 

```









