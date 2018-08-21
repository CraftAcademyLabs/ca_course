We need two extra variables where we will store the results of our calculations.

 - `bmiValue` which holds the value of the calculated BMI
 - `bmiMessage` to hold the result's message

``javascript
// src/pages/calculator/calculator.ts

export class CalculatorPage {
  ...
  bmiValue: number;
  bmiMessage: string;

  ...
}
```

Then, add the following implementation of the calculateBMI function

```javascript

// src/pages/calculator/calculator.ts

...

calculateBMI() {
  if (this.weight > 0 && this.height > 0) {
    let finalBmi = this.weight / (this.height / 100 * this.height / 100);
    this.bmiValue = parseFloat(finalBmi.toFixed(2));
    this.setBMIMessage();
  }
}

// setBMIMessage will set the text message based on the value of BMI
private setBMIMessage() {
  if (this.bmiValue < 18.5) {
    this.bmiMessage = "Underweight"
  }

  if (this.bmiValue > 18.5 && this.bmiValue < 25) {
    this.bmiMessage = "Normal"
  }

  if (this.bmiValue > 25 && this.bmiValue < 30) {
    this.bmiMessage = "Overweight"
  }

  if (this.bmiValue > 30) {
    this.bmiMessage = "Obese"
  }
}

...
```

We're almost there, last thing we need to do is add some markup on our template to display the results of our calculations. Add the following snippet to `src/pages/calculator/calculator.html`

```html
<ion-content> <!-- this line is just here to indicate where to type the following block -->
  ...

  <ion-card *ngIf="bmiValue">
    <ion-card-header>
      BMI Calculation
    </ion-card-header>
    <ion-card-content>
      <p>Person: Weight {{this.weight}} kg, Height {{this.height}} cm</p>
      <p>BMI: {{bmiValue}}</p>
      <p><strong>You are {{bmiMessage}}</strong></p>
    </ion-card-content>
  </ion-card>
</ion-content> <!-- this line is just here to indicate where to type the following block -->
```

Note that in the above code, we're conditioning the display of the result. The card will only be visible once we actually have a result on the `bmiValue` variable. Now take a look at your application in your browser and try to perform some calculations.

![Bmi Calculator](/images/bmi-calculations.png)

## Wrapping up

That's it, we just built our first mobile application using the ionic framework. We have barely scratched the surface of what's possible with the ionic framework and there is lots more cool stuff you can do with it. But you should now have a basic understanding of it, at least enough to be able to continue building more cool features into this app.

Add a new feature, or add a cool design to the app. You decide. We recommend that you skim through the [Ionic documentation](https://ionicframework.com/docs/) and see what else you can add to your app. You will probably find some inspiration there.