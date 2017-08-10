# Connecting the dots - step 4

It's time to connect the dots and get the two applications to talk to each other.

First we need to deploy the Rails application to a production server. As usual, we'll be using the simplest solution available.

Let's create a Heroku application

```
$ heroku create ca-cooper-api
  # where 'ca' are your initials
```

Now let's create a database and push the app to Heroku.

```
$ heroku addons:create heroku-postgresql
$ git push heroku master
$ heroku run rake db:migrate
```

You can manually test the API using [Postman](https://www.getpostman.com/) by doing a POST request to the registration endpoint.

**As a matter of fact, you need to create at least one user this way in order to move forward.**

![Register a user](/images/cooper_api_postman_sucess.png)  
And if you try to send the request again, that should fail.  
![Registration failure](/images/cooper_api_postman_failure.png)

Alright, if that works we can now shift our focus to the Ionic application, and we'll update it to be able to communicate with the backend.

### Authenticating to our backend

We will be using [Angular2-Token](https://github.com/neroniaky/angular2-token) - a token based authentication module for Angular that works really well with `devise_token_auth`.

We'll start by installing the library using `npm`. Run the install command from your Terminal.

```
$ npm install angular2-token --save
$ npm install @angular/router --save # we install this as it is required by the package above
```

Next we need to import and add `Angular2TokenService` to our main module. Note that `Angular2TokenService` depends on `HttpModule` and `RouterModule`, so make sure you import them too in `src/app/app.module.ts`

```typescript
import { HttpModule } from '@angular/http';
import { RouterModule } from '@angular/router';
import { Angular2TokenService } from 'angular2-token';

...

@NgModule({
  imports: [
    ...
    HttpModule,
    RouterModule,
    ...
  ],
  ...
  providers: [
    ...
    Angular2TokenService
  ]
})
```

Next, we have to Inject `Angular2TokenService` into our main component and call its `.init()` function. Remember to import it into the file first.

> **Note**: Setup instruction for `Angular2-token` are available on their readme on GitHub

Update the constructor of the main component \(`src/app/app.component.ts` \) to look like:

```typescript
constructor(
  public platform: Platform,
  public statusBar: StatusBar,
  public splashScreen: SplashScreen,
  private _tokenService: Angular2TokenService
) {
  this._tokenService.init({
    apiBase: 'https://your-cooper-api.herokuapp.com/api/v1'
  });
  
  this.initializeApp();
  
  // used for an example of ngFor and navigation
  ...
}
```

Now that we have this service setup, we'll add the ability for a user to login and logout.

### Add login ability

Add the login and logout buttons on the menu

```html
<ion-list>
  ...
  <button ion-item (click)="loginPopUp()" *ngIf="!currentUser">
    Login
  </button>
  <button ion-item (click)="logout()" *ngIf="currentUser">
    Logout {{currentUser.email}}
  </button>
</ion-list>
```

Add the matching `loginPopUp()` and `logout()` functions in the `app.component.ts` file. Also note that in our markup above, we're making use of a `currentUser` variable. This variable will hold the current user's details once logged in.

```typescript
import { ..., AlertController } from 'ionic-angular';

...

export class MyApp {
  ...
  pages: Array<{title: string, component: any}>;
  currentUser: any;

  constructor(
    ...
  ) {
    ...
  }

  ...

  loginPopUp() {
    console.log('popup');
    let confirm = this.alertCtrl.create({
      title: 'Login',
      inputs: [
        {
          name: 'email',
          placeholder: 'email'
        },
        {
          name: 'password',
          placeholder: 'password',
          type: 'password'
        }
      ],
      buttons: [
        {
          text: 'Cancel',
          handler: data => {
            console.log('Cancel clicked');
          }
        },
        {
          text: 'Login',
          handler: data => {
            this.login(data);
          }
        }
      ]
    });
    confirm.present();
  }

  login(credentials) {
    this.tokenService
      .signIn(credentials)
      .subscribe(
      res => (this.currentUser = res.json().data),
      err => console.error('error')
      );
  }

  logout() {
    this.tokenService
      .signOut()
      .subscribe(res => console.log(res), err => console.error('error'));
    this.currentUser = undefined;
  }
}
```



At this stage we have a method to login the user. The next step will be to add an interface to create and update users. That is, however, something that we leave up to you. Just a friendly reminder, make sure that you read the [Angular2-Token](https://github.com/neroniaky/angular2-token) documentation. Everything you need to know is well documented in the README file of the project.

