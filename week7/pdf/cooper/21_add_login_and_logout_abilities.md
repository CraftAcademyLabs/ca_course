Add the login and logout buttons on the menu
```javascript
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
Add the matching `loginPopUp()` and `logout()` functions in the app.component.ts file. Also note that in our markup above, we're making use of a currentUser variable. This variable will hold the current user's details once logged in.

```javascript
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
    this._tokenService
      .signIn(credentials)
        .subscribe(
         res => (this.currentUser = res.json().data),
         err => console.error('error')
      );
  }

  logout() {
    this._tokenService
      .signOut()
      .subscribe(res => console.log(res), err => console.error('error'));
    this.currentUser = undefined;
  }
}
```
At this stage we have a method to login the user. The next step will be to add an interface to create and update users. That is, however, something that we leave up to you. Just a friendly reminder, make sure that you read the `Angular2-Token` documentation. Everything you need to know is well documented in the README file of the project.