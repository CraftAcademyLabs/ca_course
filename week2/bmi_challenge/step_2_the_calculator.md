---
title: "Step 2, The calculator"
subtitle: "BMI challenge, Week 2"
author: [Craft Academy - Coding as a Craft]
date: Version 0.1
subject: "Javascript, BMI"
keywords: [Javascript, BMI]
titlepage: true
titlepage-color: f28e24
titlepage-text-color: "FFFFFF"
titlepage-rule-color: "FFFFFF"
titlepage-rule-height: 2
...

## The calculator


Create a spec file for the `BMICalculator` and call it `bmi_calculator_spec.js` 

Add the following code to it

```javascript
# spec/bmi_calculator_spec.js

describe("BMICalculator", function() {
  var bmi_calculator;
  var person;

  beforeEach(function() {
    person = new Person({weight: 90, height: 186});
    calculator = new BMICalculator();
  });

  it("calculates BMI for a person using metric method", function() {
    calculator.metric_bmi(person);
    expect(person.bmiValue).toEqual(26.01);
  });

});
```

Make sure that you run your spec runner after each addition and read the error messages. 

Add the following code to the `src/bmi_calculator.js` file

```javascript
# src/bmi_calculator.js

function BMICalculator(){
};

BMICalculator.prototype.metric_bmi = function(obj) {
  var weight = obj.weight;
  var height = obj.height;
  if (weight > 0 && height > 0) {
    var finalBmi = weight / (height / 100 * height / 100);
    obj.bmiValue =  parseFloat(finalBmi.toFixed(2));
  }
};
```
Why do we need to condition the `finalBMI`? (Why do we need an `if` statement?)

Run your tests again.


We still have not set the `bmiMessage` on the `Person`. In order to do that, we need to create a function that sets that message depending on what `bmiValue` the person has.

Add this function to the `bmi_calculator.js` file.

```javascript
# src/bmi_calculator.js

function setBMIMessage (obj){
  if (obj.bmiValue < 18.5) {
    obj.bmiMessage = "Underweight"
  }
  if (obj.bmiValue > 18.5 && obj.bmiValue < 25) {
    obj.bmiMessage = "Normal"
  }
  if (obj.bmiValue > 25 && obj.bmiValue < 30) {
    obj.bmiMessage = "Overweight"
  }
  if (obj.bmiValue > 30) {
    obj.bmiMessage = "Obese"
  }
}
```

Remember to call it from the `metric_bmi` function.

```javascript
# src/bmi_calculator.js

BMICalculator.prototype.metric_bmi = function(obj) {
  var weight = obj.weight;
  var height = obj.height;
  if (weight > 0 && height > 0) {
    var finalBmi = weight / (height / 100 * height / 100);
    obj.bmiValue =  parseFloat(finalBmi.toFixed(2));
    setBMIMessage(obj);
  }
};
```

### Review and reflect
1. Why does the `setBMIMessage(obj);` call do?
2. Why do we have a separate function called `setBMIMessage`? 


