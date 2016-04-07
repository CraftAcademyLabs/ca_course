# Connecting the dots

It's time to connect the dots and get the two applications to talk to eachother. 

First we need to deploy the Rails application to a production server. 

Let's create a Heroku application
```
$ heroku create ca-cooper-api 
  # where 'ca' are your initials
```
To enable features such as static asset serving and logging on Heroku please we need to add the `rails_12factor` gem to your `Gemfile`.

!FILENAME Gemfile
```ruby
# [...]
group :production do
  gem 'rails_12factor'
end
```
Make sure to run `bundle` and commit all your changes (You have been making commits during this walk through, right?).

Now let's create a database and push the app to Heroku.

```
$ heroku addons:create heroku-postgresql
$ git push heroku master
$ heroku run rake db:migrate
```
You can manually test the API using Postman by doing a POST request to the registration endpoint. 

![Register a user](/images/cooper_api_postman_sucess.png)
And if you try to send the request again, that should fail.
![Registration failure](/images/cooper_api_postman_failure.png)

Alright, if that works we should shift our focus to the Ionic application. 

We will be using [ng_token_auth](https://github.com/lynndylanhurley/ng-token-auth) - a token based authentication module for AngularJS that works really well wit `devise_token_auth`.

We'll start by installing the library using Bower. Run the install command frpm your Terminal. 
```
$ bower install ng-token-auth --save
```
Make sure that `angular-cookie`, and `ng-token-auth` are included in your `index.html`.


!FILENAME www/index.html
```html
<!-- ionic/angularjs js -->
<script src="lib/ionic/js/ionic.bundle.js"></script>
<script src="lib/angular/angular.js"></script>
<script src="lib/angular-cookie/angular-cookie.js"></script>
<script src="lib/ng-token-auth/dist/ng-token-auth.js"></script>
``` 

Include `ng-token-auth` in your module's dependencies and add a basic configuration.

!FILENAME www/js/app.js
```javascript
angular.module('starter', ['ionic', 'starter.controllers', 'ng-token-auth'])
    .config(function($authProvider) {
        $authProvider.configure({
            apiUrl: 'https://ca-cooper-api.herokuapp.com/api/v1/'
        });
    })
```

In `controllers.js` `AppCtrl` locate the `doLogin()` method.

!FILENAME www/js/controllers.js
```javascript
//...
// Perform the login action when the user submits the login form
$scope.doLogin = function () {
  $auth.submitLogin($scope.loginData)
    .then(function (resp) {
      // handle success response
      $scope.closeLogin();
    })
    .catch(function (error) {
      // handle error response
      $scope.errorMessage = error;
    });
};
//...
```
Now, we need to make some small changes and additions to out view templates.

Change the input fiels `Username` to `Email` 

!FILENAME www/templates/login.html
```html
//...
<label class="item item-input">
  <span class="input-label">Email</span>
  <input type="text" ng-model="loginData.email">
</label>
//...
```
And add a placeholder for display of error messages
!FILENAME
```html
//...
<ion-content>
  <div class="row" ng-if="errorMessage">
    <p ng-repeat="error in errorMessage.errors">
      {{error}}
    </p>
  </div>
  //...
```









