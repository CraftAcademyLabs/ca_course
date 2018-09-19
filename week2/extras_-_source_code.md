---
title: "Extras - Source code"
subtitle: "Week 2"
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

## Extras - Source code

If you haven't completed the BMI Challenge, you can get the code for the `person.js` and the `bmi_calculator.js` right here. Make sure you place them in the right folder.

If you HAVE your own code, use that code. Also, if you plan to implement an extended UI, including the Imperial method, the code below will not work. 

#### person.js
```javascript
function Person(attr) {
  this.weight = attr.weight;
  this.height = attr.height;
};

Person.prototype.calculate_bmi_met = function() {
  calculator = new BMICalculator();
  calculator.metric_bmi(this);
};

Person.prototype.calculate_bmi_imp = function() {
  calculator = new BMICalculator();
  calculator.imperial_bmi(this);
};

```

#### bmi_calculator.js
```javascript
function BMICalculator(){
};

BMICalculator.prototype.metric_bmi = function(obj) {
  var weight = obj.weight;
  var height = obj.height;
  if (weight > 0 && height > 0) {
    var finalBmi = weight / (height / 100 * height / 100);
    obj.bmiValue =  parseFloat(finalBmi.toFixed(2));
    setBMIMessage(obj);
  }
};

BMICalculator.prototype.imperial_bmi = function(obj) {
  var weight = obj.weight;
  var height = obj.height;
  if (weight > 0 && height > 0) {
    var finalBmi = (weight * 703 ) / (height * height);
    obj.bmiValue =  parseFloat(finalBmi.toFixed(2));
    setBMIMessage(obj);
  }
};
function setBMIMessage (obj, value){
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


