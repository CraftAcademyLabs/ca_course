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



