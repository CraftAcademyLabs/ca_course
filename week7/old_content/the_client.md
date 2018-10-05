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

### First time working with services/providers

Now that we have our interface setup with all the necessary inputs, we need to implement the actual logic to perform the calculations. This is where the code base we pointed you to at the first step will come to play. Now we could simply get that code and add it directly to our component. We did something similar in the previous challenge \(BMI Calculator\). This is okay for small applications, but as your app grows, it becomes difficult to maintain it. We want to be as modular as we can generate components that do one thing and do it well. In software development this is often referred to as the [Single Responsibility principle](https://en.wikipedia.org/wiki/Single_responsibility_principle). Following these kind of patterns while building your applications will lead to better software, that is easy to maintain and extend.

With this in mind, how do we then go about integrating our logic to the application? You might think, oh! let's just generate a new component for this. That's not entirely wrong, but, you really want to make use of components when you have some visual aspect to them. When it comes to business logic, we can encapsulate them in services. So, we'll create a service which role in our application will be to perform the calculations we need. Then we'll use that service in our page component. The component doesn't need to know how the calculations are made. The service here is like a black box, to which we will feed some information \(i.e. gender, distance, etc...\) and it'll return the assessments results to the component.

So how do we add services to an ionic application? We can do so by generating a provider. In your terminal run the following command:

```shell
$ ionic g provider person
```

Adjust the provider `src/providers/person/person.ts` to look like this:

```typescript
import { Injectable } from '@angular/core';
import 'rxjs/add/operator/map';

import { CooperProvider } from '../cooper/cooper';

@Injectable()
export class PersonProvider {
  public gender: string;
  public age: number;
  public assessmentMessage: string;

  constructor(private cooper: CooperProvider) {}

  doAssessment(distance: number): void {
    this.assessmentMessage = this.cooper.assess(this, distance);
  }
}
```

Note that we have imported another provider called `CooperProvider` that we need to define. Let's generate it.

```shell
$ ionic g provider cooper
```

Then add edit it to look like this:

```typescript
import { Injectable } from '@angular/core';
import 'rxjs/add/operator/map';

@Injectable()
export class CooperProvider {
  constructor() {
    console.log('Hello CooperProvider Provider');
  }

  private ratings: any = [
    'Excellent',
    'Above average',
    'Average',
    'Below average',
    'Poor'
  ];

  private cooperTable: any = {
    female: {
      '13-14': ['>2000', '1900-1999', '1600-1899', '1500-1599', '<1500'],
      '15-16': ['>2100', '2000-2099', '1700-1999', '1600-1699', '<1600'],
      '17-19': ['>2300', '2100-2299', '1800-2099', '1700-1799', '<1700'],
      '20-29': ['>2700', '2200-2699', '1800-2199', '1500-1799', '<1500'],
      '30-39': ['>2500', '2000-2499', '1700-1999', '1400-1699', '<1400'],
      '40-49': ['>2300', '1900-2299', '1500-1899', '1200-1499', '<1200'],
      '50+': ['>2200', '1700-2199', '1400-1699', '1100-1399', '<1100']
    },
    male: {
      '13-14': ['>2700', '2400-2699', '2200-2399', '2100-2199', '<2100'],
      '15-16': ['>2800', '2500-2799', '2300-2499', '2200-2299', '<2200'],
      '17-19': ['>3000', '2700-2999', '2500-2699', '2300-2499', '<2300'],
      '20-29': ['>2800', '2400-2799', '2200-2399', '1600-2199', '<1600'],
      '30-39': ['>2700', '2300-2699', '1900-2299', '1500-1999', '<1500'],
      '40-49': ['>2500', '2100-2499', '1700-2099', '1400-1699', '<1400'],
      '50+': ['>2400', '2000-2399', '1600-1999', '1300-1599', '<1300']
    }
  };

  public assess(person, distance: number): string {
    return this.getRating(person, distance);
  }

  private ageRange(age: number): string {
    switch (true) {
      case age >= 13 && age <= 14:
        return '13-14';
      case age >= 15 && age <= 16:
        return '15-16';
      case age >= 17 && age <= 19:
        return '17-19';
      case age >= 20 && age <= 29:
        return '20-29';
      case age >= 30 && age <= 39:
        return '30-39';
      case age >= 40 && age <= 49:
        return '40-49';
      case age >= 50:
        return '50+';
      default:
        return 'invalid range';
    }
  }

  private getRating(person, distance): string {
    const ageRange = this.ageRange(person.age);

    if (ageRange === 'invalid range') {
      return 'Invalid age range';
    }

    const distanceRanges = this.cooperTable[person.gender.toLowerCase()][
      ageRange
    ];

    let ratingIndex: number;

    distanceRanges.forEach((dRange, index) => {
      if (
        (dRange.match(/>\d*/) && distance >= parseInt(dRange.slice(1), 10)) ||
        (dRange.match(/<\d*/) && distance < parseInt(dRange.slice(1), 10))
      ) {
        ratingIndex = index;
      } else {
        const minMax = dRange.split('-');
        const min = parseInt(minMax[0], 10);
        const max = parseInt(minMax[1], 10);

        if (distance >= min && distance <= max) {
          ratingIndex = index;
        }
      }
    });

    return this.ratings[ratingIndex];
  }
}
```

Go over to the root module, `src/app/app.module.ts`, you will realize that the providers we generated are automatically imported.

We now have our two providers, let's update the `calculate()` function to make use of them in order to perform the assessment. Back in `src/pages/home/person.ts`, import the `PersonProvider` at the top of the file

```typescript
import { PersonProvider } from '../../providers/person/person';
```

Then update your `constructor` and `calculate` function as follows:

```typescript
...
constructor(
  public navCtrl: NavController,
  public person: PersonProvider
) {
  this.user = { distance: 1000, age: 20, gender: 'female' };
}

calculate() {
  this.person.age = this.user.age;
  this.person.gender = this.user.gender;

  this.person.doAssessment(this.user.distance);
  console.log(this.person.assessmentMessage);
}
```

If you now head back to your your browser and set some values then press the calculate button, you should see the result printed in your browser's console. Next, we will add some markup to our page to display the result to our user.

### Display the result on our page

We'll add some markup to the page template and this section of the page should only be displayed once the result of our calculations are available. Add the following snippet just before the closing `</ion-content>`

```html
<ion-card *ngIf="person?.assessmentMessage">
  <ion-card-header>
    Cooper Test Results
  </ion-card-header>
  <ion-card-content>
    Gender: {{person?.gender}}, Age: {{person?.age}} <br/> Result: {{person?.assessmentMessage}}
  </ion-card-content>
</ion-card>
```

Let's see if that works! Head over to your browser and give it a try. You should now see the results of the test displayed on the page as follows:

![](/assets/ionic/cooper-display-result.png)

Feel free to play around with different values.

Now that the basic functionality of our application is in place. We can move to the next steps which will involve connecting it to our backend to save the results of our calculations for every users.

