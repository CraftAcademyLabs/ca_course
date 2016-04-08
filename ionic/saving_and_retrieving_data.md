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
<!-- ionic/angularjs js -->
/...
<script src="lib/angular-resource/angular-resource.js"></script>
/...
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

.factory('performaceData', function ($resource, API_URL) {
  return $resource(API_URL + '/data', {});
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

Let's start with saving the data. On our view where we do the calculations, we want to display a button that calls the `saveData` function. But we only want to display that button IF there is a user logged in and the calculation has been performed. 

Modify your `test.html` template with this code. 

!FILENAME www/templates/test/test.html
```html 
 <div ng-if="person">
    <div class="card">
      <div class="item item-divider">
        Cooper Test results
      </div>
      <div class="item item-text-wrap">
        <p>Person: Age {{person.age}}, Gender {{person.gender}}</p>
        <p>Result: {{person.cooperMessage}}</p>
      </div>
    </div>
    <button
      ng-if="currentUser"
      ng-controller="PerformanceCtrl"
      class="button button-full button-calm"
      ng-click="saveData(person)">Save results
    </button>
 </div>
```

Let's build our `saveData` function with a success ans an error fallback. It can look something like this for the moment. 

!FILENAME 

```javascript
$scope.saveData = function(person){
  data = {performace_data: {data: {message: person.cooperMessage}}}
  performaceData.save(data, function(response){
    console.log(response);
  }, function(error){
    console.log(error);
  })
};
```

If you try this out in the browser while having the console open, you'll see that you'll get an `Unauthorized` error.

![](/images/cooper_api_error_1.png)

**Well, that's a bit of a problem but we'll solve it. At least we know that the API endpoint is within our reach. That IS good progress!**

So, let's make this work.

The reason we are getting a 401 on the request is becouse we are not sending any credentials with the request and thus we can not get authorized. 

Let's make sure that we get the necessary info stored in the `currentUser` object. We need to modify the way we store that information. 

Localize the `'auth:login-success'` function in our `AppCtrl`. We will make a change to store access-token, uid, etc by grabbing that info from the response headers. 

!FILENAME www/js/controllers.js
```javascript
$rootScope.$on('auth:login-success', function (ev, user) {
  $scope.currentUser = angular.extend(user, $auth.retrieveData('auth_headers'));
});

```

We also want to add `$ionicLoading, $ionicPopup` to our `PerformanceCtrl`. We will use them those methods to give our user feedback on the requests progress. 

We will also add an `showAlert()` function and refactor our `saveData()` function. Examine the code below to fully understand what it does before you implement it. 

!FILENAME www/js/controllers.js
```javascript
//...
.controller('PerformanceCtrl', function($scope, performaceData, $ionicLoading, $ionicPopup){

  $scope.saveData = function(person){
    var data = {performance_data: {data: {message: person.cooperMessage}}};
    $ionicLoading.show({
      template: 'Saving...'
    });
    performaceData.save(data, function(response){
      $ionicLoading.hide();
      $scope.showAlert('Sucess', response.message);
    }, function(error){
      $ionicLoading.hide();
      $scope.showAlert('Failure', error.statusText);
    })
  };

  $scope.retrieveData = function(){
  //Still not implemented...
  };

  $scope.showAlert = function(message, content) {
    var alertPopup = $ionicPopup.alert({
      title: message,
      template: content
    });
    alertPopup.then(function(res) {
    // Place some action here if needed...
    });
  };
})
//...

```







