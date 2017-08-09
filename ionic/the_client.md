# The Client - Step 3

We will be using Ionic to build our mobile client. If you are not familiar with Ionic yet, please go back in the documentation and complete the [Going mobile with Ionic](https://craftacademy.gitbooks.io/coding-as-a-craft/content/going_mobile_with_ionic.html) chapter.

The objective of this step is to allow the user to calculate his results. We will NOT be accessing the API yet. The login functionality will not work once we are finished with this step. That will be the objective of the next chapter.

### Set up

Last time we generated a new ionic applicatin using the tabs menu, let's do something different this time. For this app we will be using the `sidemenu` template.

Run this command it your terminal to create the application.

```
$ ionic start cooper_client sidemenu
```

Navigate into the new folder \(`cooper_client`\) and let's start the application to see what it looks like.

```
$ ionic serve --lab
```

If you see something that looks like this, congratulations, you have just generated your second ionic application.

![](/assets/ionic/cooper-scaffolded-app.png)

### Where is the side menu coming from?

Can you try find out where this menu is located? Look within the \`src\` directory.

Since the menu is available to the entire application, no matter the page we are in, then our first guess would be that it is at the template the is associated with the root component - this assumption is actually right. Our root component is called `app.component.ts` with the matching template being `app.html` - you can tell this by looking at the metadata `selector`

The menu is within the `ion-content` tab

```html
<ion-content>
  <ion-list>
    <button menuClose ion-item *ngFor="let p of pages" (click)="openPage(p)">
      {{p.title}}
    </button>
  </ion-list>
</ion-content>
```

From the above code, we can tell there is a variable called `pages` in our component that we are looping through using the directive `*ngFor` then attaching `click` event to a function in the component.

#### `pages` variable

Head over to the component and locate the `pages` variable

```typescript
pages: Array<{title: string, component: any}>;
```

This declares a variable called `pages` that is an `Array`. This array contains objects with `title` and `component`. Inside ur constructor, the variable is then assigned an array containing two objects.

```typescript
this.pages = [
  { title: 'Home', component: HomePage },
  { title: 'List', component: ListPage }
];
```

### Cleaning up

#### Remove the list page

We will now clean up our application a bit by removing some sections that we do not need. Let's get started by deleting the `src/pages/list` folder. Go ahead and remove it from the menu list \(do this from the `app.component.ts` file\)

Remove its imports from the `app.component.ts`. On the `app.module.ts` file remove it from the imports and from the decorator too \(`entryComponents` and `declarations`\) then make sure you are not getting any errors in your terminal



#### Remote homepage content

Head over to the hompage template and remove everything withing the `ion-content` tag, afterwhich you will see an empty page on the browser

_**Hint**_: Homepage template is located at `src/pages/home/home.html`

The template should now look like this

```html
<ion-header>
  <ion-navbar>
    <button ion-button menuToggle>
      <ion-icon name="menu"></ion-icon>
    </button>
    <ion-title>Home</ion-title>
  </ion-navbar>
</ion-header>

<ion-content padding>
</ion-content>
```

If you head over to your browser you should see something like this.  
![](/assets/ionic/cooper-clean-home.png)  
This will do for now. We will add the Login functionality further down the road. Right now, we want to focus on the main functionality of this application - to calculate the test results and give the user some feedback.



### Accepting user input

At this stage we have a good skeleton app to get started with so we can add a form that asks a user for some details so our app will look like this:

![](/assets/Screenshot 2017-08-09 14.30.29.png)

Type the following markup to your `home.html` file within the `ion-content` tag

```html
...
<ion-list>
  <ion-item>
    <ion-label floating>Distance</ion-label>
    <ion-input type="number" [(ngModel)]="user.distance"></ion-input>
  </ion-item>
  <ion-item>
    <ion-label>Gender</ion-label>
    <ion-select [(ngModel)]="user.gender">
      <ion-option value="female">Female</ion-option>
      <ion-option value="male">Male</ion-option>
    </ion-select>
  </ion-item>
  <ion-item>
    <ion-label>Age:
      <ion-badge *ngIf="user.age"> {{user.age}}</ion-badge>
    </ion-label>
    <ion-range min="10" max="120" [(ngModel)]="user.age" >
      <ion-label range-left>10</ion-label>
      <ion-label range-right>120</ion-label>
    </ion-range>
  </ion-item>
</ion-list>

<button block ion-button (click)="calculate()">Calculate</button>
...
```

At this stage, if you visit the app in your browser, you will get an error about not being able to read property `distance` or `undefined`. This is because we haven't defined a `user` in our component. Let's fix that. We can assign an empty object to it for now.

```typescript
export class HomePage {
  user: any = {};
  ...

}
```

Save this change and head back to your browser and everything should work now. 

Click on the button, what happens? Why do you think it fails? Read through the error and try to figure out what the problem might be here.

> **IMPORTANT** Try to answer this before you move on to the next step.



Incase you were not able to know why it failed, it it because the function tied to the button is not yet defined in the component; let's define it:

```typescript
export class HomePage {
  user: any = {};

  constructor(public navCtrl: NavController) {}

  calculate() {
    console.log(this.user);
  }
}
```

Our function currently logs out the `user` object everytime you click on the button. Open your developer tools console on the browser and play around.



Did you realize that the app starts with every fields empty? We can give it default values that can then be used when the app starts, we do this by initializing `user` within our constructor function

```typescript
export class HomePage {
  ...
  constructor(public navCtrl: NavController) {
    this.user = { distance: 1000, age: 20 };
  }

  ...
}
```

![](/assets/ionic/cooper-intial-values.png)

