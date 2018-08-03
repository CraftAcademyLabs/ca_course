We will be using `Angular2-Token` - a token based authentication module for Angular that works really well with devise_token_auth.

We'll start by installing the library using npm. Run the install command from your Terminal.
```shell
$ npm install angular2-token --save
$ npm install @angular/router --save # we install this as it is required by the package above
```
When you fire up the server you might get this error.

![@ionic/lab missing ](/images/ionic_lab_missing_error.png)

You solve this by installing this package.
```shell
$ npm install @ionic/lab
```

Next we need to import and add `Angular2TokenService` to our main module. Note that `Angular2TokenService` depends on `HttpModule` and `RouterModule`, so make sure you import them too in `src/app/app.module.ts`
```javascript
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
Next, we have to Inject `Angular2TokenService` into our main component and call its .`init()` function. Remember to import it into the file first.

Note: Setup instruction for` Angular2-token` are available on their readme on GitHub

Update the constructor of the main component (`src/app/app.component.ts`) to look like:
```javacript
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
