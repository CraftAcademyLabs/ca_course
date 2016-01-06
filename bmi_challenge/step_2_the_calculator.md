## The calculator


Create a spec file for the `BMICalculator` and call it `bmi_calculator_spec.js` 

Add the following code to it

```js
describe("BMICalculator", function() {
  var bmi_calculator;
  var person;

  beforeEach(function() {
    person = new Person(90, 186);
    calculator = new BMICalculator();
  });

  it("caluculates BMI for a person using metric method", function() {
    calculator.metric_bmi(person);
    expect(person.bmiValue).toEqual(26.01);
  });

});
```

Make sure that you run your spec runner after each addition and read the error messages. 

Add the following code to the `src/bmi_calculator.js` file

```js
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

