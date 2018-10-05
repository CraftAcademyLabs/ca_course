Okay, let's move on to implementing the BMI Calculator. Let's create a form on the `calculator.html` template that will allow the user to input his/her weight and height and return the calculated values.

```html

<!-- type the following content inside <ion-content> tag -->
<ion-list>
  <ion-item>
    <ion-label floating>Weight (kg)</ion-label>
    <ion-input type="number" [(ngModel)]="weight"></ion-input>
  </ion-item>

  <ion-item>
    <ion-label floating>Height (cm)</ion-label>
    <ion-input type="number" [(ngModel)]="height"></ion-input>
  </ion-item>
</ion-list>

<div padding>
  <button block ion-button (click)="calculateBMI()">Calculate</button>
</div>

```

Next, in `scr/pages/calculator/calculator.ts` we'll create two variables, `weight` and `height` that we will use to get information from our view. These two variables should be of type `number`. We also need to declare a function `calculateBMI` in which we'll add some logic later on to calculate the BMI of an individual.

```javascript
// src/pages/calculator/calculator.ts
// Edit your file to have the following code

import { Component } from '@angular/core';
import { IonicPage } from 'ionic-angular';

@IonicPage()
@Component({
  selector: 'page-calculator',
  templateUrl: 'calculator.html',
})
export class CalculatorPage {
  height: number;
  weight: number;

  constructor() {
  }

  calculateBMI() {
    // Add logic to calculate BMI here
  }
}
```

Now we need to add some logic to this app in order to perform the actual calculations. The code for this is not that different from what you wrote in the BMI challenge during the Prep Course. See if you can use your implementation from that week into this app ;-)

In the next section, we'll show you one way of solving this.