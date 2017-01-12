# The Client - Step 3

We will be using Ionic to build our mobile client. If you are not familiar with Ionic yet, please go back in the documentation and complete the [Going mobile with Ionic](https://craftacademy.gitbooks.io/coding-as-a-craft/content/going_mobile_with_ionic.html) chapter.

The objective of this step is to allow the user to calculate his results. We will NOT be accessing the API yet. The login functionality will not work once we are finished with this step. That will be the objective of the next chapter. 

###Set up

For this app we will be using the `sidemenu` template. 

Run this command it your terminal to create the application.
```
$ ionic start cooper_client sidemenu
```

`cd` into the new folder (`cooper_client`) and install the `npm` and `bower` dependencies.
```
$ npm install
$ bower install
```

> Note: If you don't have **`bower`** installed on your computer you can install it with **`npm install -g bower`**


Once that is ready, you might want to start the Ionic server to see if everything is set up correctly.
```
$ ionic serve -c --lab
```
You should see something like this.

![Ionic Sidemenu template](/images/ionic-sidemenu-template.png)

###Clean up the code

The template comes with a lot of scaffold functions and views that we will not be using. 

Open the code in your editor and delete the `PlaylistsCtrl` and `PlaylistCtrl` from the `www/js/controllers.js` file.

You also want to remove all files in the `www/templates` folder except the `login.html` and `menu.html`.

Edit the `menu.html` and delete unused menu items. Keep the `Login` item

!FILENAME www/templates/menu.html
```html
<ion-content>
  <ion-list>
    <ion-item menu-close ng-click="login()">
      Login
    </ion-item>
    <!-- Detlete this block below: -->
    <ion-item menu-close href="#/app/search">
      Search
    </ion-item>
    <ion-item menu-close href="#/app/browse">
      Browse
    </ion-item>
    <ion-item menu-close href="#/app/playlists">
      Playlists
    </ion-item>
    <!-- end of deleted block -->
  </ion-list>
</ion-content>
```

Finally, in the `app.js` file, delete unused routes. We will also ADD a route to a new `About` view.  Make sure to modify the default route to the new `/app/about` route. Your `.config` block should look like this.

!FILENAME www/js/app,js
```javascript
.config(function($stateProvider, $urlRouterProvider) {
  $stateProvider

  .state('app', {
    url: '/app',
    abstract: true,
    templateUrl: 'templates/menu.html',
    controller: 'AppCtrl'
  })

  .state('app.about', {
    url: '/about',
    views: {
      'menuContent': {
        templateUrl: 'templates/about/about.html'
      }
    }
  });
  // if none of the above states are matched, use this as the fallback
  $urlRouterProvider.otherwise('/app/about');
});
```
Create a new folder named `about` in the `www/templates` folder. We will place a new template called `about.html` in that folder.

!FILENAME www/templates/about/about.html
```html
<ion-view title="About">
  <ion-content>
    <div class="row">
      <div class="col">
        Craft Academy Cooper Test Challenge - Mobile Client.
      </div>
    </div>
  </ion-content>
</ion-view>
```
If you head over to your browser you should see something like this.
![The new About view](/images/cooper-mobile-about-template.png)
This will do for now. We will add the Login functionality further down the road. Right now, we want to focus on the main functionality of this application - to calculate the test results and give the user some feedback. 

###Adding the logic

The next step is to add the `cooper.js` code to your application.
1. Import the code and place it in the `www/js` folder.
2. Include it in the main application view (`www/index.html`)

###The test view

Create a template for the test view in the `www/templates/test/ folder (you need to create it first). Call it `test.html`and add the following markup to it.

!FILENAME www/templates/test/test.html
```html
<ion-view title="Cooper Test">
  <ion-content>
    <div class="row">
      <div class="col">
        <div class="list">
          <label class="item item-input">
            <input type="text" class="item item-input" placeholder="Gender" ng-model="data.gender">
          </label>
          <label class="item item-input">
            <input type="number" class="item item-input" placeholder="Age" ng-model="data.age">
          </label>
          <label class="item item-input">
            <input type="number" class="item item-input" placeholder="Distance" ng-model="data.distance">
          </label>
            <button class="button button-full button-calm" ng-click="calculateCooper()">Send</button>
        </div>


        <div class="card" ng-if="person">
          <div class="item item-divider">
            Cooper Test results
          </div>
          <div class="item item-text-wrap">
            <p>Person: Age {{person.age}}, Gender {{person.gender}}</p>
            <p>Result: {{person.cooperMessage}}</p>
          </div>
        </div>
      </div>

    </div>

  </ion-content>
</ion-view>

```

Create a new route in the `www/js/app.js` to point to the template and a  new controller we will be creating in a minute.

!FILENAME www/js/app.js
```javascript
  .state('app.test', {
    url: '/test',
    views: {
      'menuContent': {
        templateUrl: 'templates/test/test.html',
        controller: 'TestController'
      }
    },
  })
```

In the `www/templates/menu.html` we need to add a new menu item so we can navigate to that view.

!FILENAME www/templates/menu.html
```html
#[...]
  <ion-item menu-close href="#/app/test">
    Cooper Test
  </ion-item>
#[...]
```

Let's focus on the controller. We need to create a new controller in `www/js/controllers.js` 

!FILENAME www/js/controllers.js
```javascript
#[...]
.controller('TestController', function($scope) {
  $scope.data = {};
  $scope.calculateCooper = function() {
    var person = new Person({
      gender: $scope.data.gender,
      age: $scope.data.age
    });
    person.assessCooper($scope.data.distance);
    $scope.person = person;
    console.log($scope.person)
  };
```

Run the app and input some test data and click `Send`. You should see the results displayed on the page. 

###The user interface

Entering values using the keyboard is always hard on a mobile device. We will do some refactoring to make the user experience a little better, although there is much more you can do on your own to make this application more appealing to the user (we are mainly focusing on the basic functionality at the moment). 

Let's change the input fields to a select field for gender and sliders for age and distance. We will also do a small refactoring of the initial values and move those settings to our controller. It feels better to have them stored there. 

Replace the input fields in the `test.html` template with this form.

!FILENAME
```html
#[...]
<form name="testdata">
  <label class="item item-input item-select">
    <div class="input-label">
      Gender
    </div>
    <select
    ng-model="data.gender"
    ng-options="option as option for option in gender"
    required></select>
  </label>

  <div class="item item-divider">Age
    <output name="agevalue" for="age"></output>
  </div>
  <div class="item range range-positive">
    <input
    name="age"
    type="range"
    min="{{ageValues.min}}"
    max="{{ageValues.max}}"
    value="{{ageValues.value}}"
    ng-model="data.age"
    oninput="agevalue.value = age.value"
    required>{{ageValues.min}}/{{ageValues.max}} yrs
  </div>

  <div class="item item-divider">Distance
    <output name="distvalue" for="distance"></output>
  </div>
  <div class="item range range-positive">
    <input
    name="distance"
    type="range"
    min="{{distanceValues.min}}"
    max="{{distanceValues.max}}"
    value="{{distanceValues.value}}"
    ng-model="data.distance"
    oninput="distvalue.value = distance.value"
    required>{{distanceValues.min}} - {{distanceValues.max}} m
  </div>

  <button
  class="button button-full button-calm"
  ng-click="calculateCooper()"
  ng-disabled="testdata.$invalid">Get results</button>
</form>
#[...]
```
And modify your controller with predefined values for `gender`, `age` and `distance` 

!FILENAME www/js/controllers.js
```javascript
#[...]
.controller('TestController', function($scope) {
  $scope.gender = ['Male', 'Female']
  $scope.ageValues = {
    min: 20,
    max: 60,
    value: 20
  };
  $scope.distanceValues = {
    min: 1000,
    max: 3500,
    value: 1000
  };
  $scope.data = {};
  #[...]
  };
```
If you run the application you should see this interface.

![Cooper Test Viev UI](/images/cooper-client-ui.png)











