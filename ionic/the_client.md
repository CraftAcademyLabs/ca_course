# The Client - Step 3
We will be using Ionic to build our mobile client. If you are not familiar with Ionic yet, please go back in the documentation and complete the [Going mobile with Ionic](https://craftacademy.gitbooks.io/coding-as-a-craft/content/going_mobile_with_ionic.html) chapter.

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
Once that is ready, you might want to start the Ionic server to see if everything is set up correctly.
```
$ ionic serve -c --lab
```
You should see something like this.

![Ionic Sidemenu template](/images/ionic-sidemenu-template.png)

###Clean up the code
The template comes with a lot of scaffold functions and views that we will not be using. 

Open the code in your editor and delete the `PlaylistsCtrl` and `PlaylistCtrl` from the `www/js/controllers.js` file.

You also want to remove all files in the `www/templates` folder accept the `login.html` and `menu.html`.

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

Finally, in the `app.js` file, delete unused routes. We will also ADD a route to a new `About` view.  Make sure to modify the default route to thet new `/app/about` route. Your `.config`block should look like this.

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





