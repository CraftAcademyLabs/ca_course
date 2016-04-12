## JavaScript basics - BMI challenge

**Body mass index (BMI) is a simple tool that is generally used to estimate the total amount of body fat. In this challenge we will write a JavaScript program that calculates a individuals BMI index.**


### How To Calculate BMI

To calculate BMI, you need to know your weight and height in kilograms and centimeters or meters (**Metric Method**). The weight of a person is then divided by the height.
 
If you know your height and weight in inches and pounds the calculation is a little more complex (**Imperial Method**).

#### Metric Method

The metric formula accepts height measurements in meters and weight in kilograms. If you know your height in centimeters only, simply divide the number of centimeters by 100 convert it to meters.

For example, a person who is 183 cm tall is 1.83 m tall (183 cm / 100 = 1.83 m).

1. Multiply your height by itself.
2. Divide your weight in kilograms by the value calculated in step 1.
3. The resulting number is your BMI. Compare this BMI value with the weight status table below.

#### Imperial Method

The imperial formula accepts height measurements in inches and weight in pounds. It's popular in the US where the imperial system is mostly used. Many people know their height in feet and inches, but not in inches only.

If this applies to you, we need to convert your height into inches so we can use it in the equation. There are 12 inches in a foot, so multiply your number of feet by 12 and add them to the number of extra inches.

For example, if your height is 5 feet 10 inches, multiply 5 by 12 (which gives 60") and add them to the extra 10 inches (which gives 70").

Now we have the right measurements we can use them in the formula.

There are three simple steps for computing BMI with imperial values:

1. Multiply your weight in pounds by 703.
2. Multiply your height in inches by itself
3. Divide the figure from step 1 by the figure in step 2.
The resulting number is your BMI. 

Compare this BMI value with the weight status table below.

#### BMI Weight Status Categories
| BMI         	| Weight Status 	|
|-------------	|---------------	|
| Below 18.5  	| Underweight   	|
| 18.5 - 24.9 	| Normal        	|
| 25 - 29.9   	| Overweight    	|
| Over 30.0   	| Obese         	|


Since you already know some ruby, here's the implementation of the Person class with a module that calculates BMI using the metric method. We enclose it here for referance only, going over that code might help you get started with your JavaScript code.

```ruby
class Person
  attr_accessor :bmi_value, :bmi_message, :weight, :height
  
  def initialize(options={})
    @weight = options[:weight]
    @height = options[:height]
  end
  
  def calculate_bmi_met
    results = BMICalculator.metric_bmi(self)
  end
end

module BMICalculator
  def self.metric_bmi(object)
    weight = object.weight.to_f
    height = object.height.to_f
    
    if weight > 0 && height > 0
      final_bmi = weight / (height / 100 * height / 100)
      object.bmi_value =  final_bmi.round(2)
      set_bmi_message(object)
    end
  end
  
  def self.set_bmi_message(object)
    if object.bmi_value < 18.5
      object.bmi_message = "Underweight"
    elsif object.bmi_value > 18.5 && object.bmi_value < 25
      object.bmi_message = "Normal"
    elsif object.bmi_value > 25 && object.bmi_value < 30
      object.bmi_message = "Overweight" 
    else object.bmi_value > 30
      object.bmi_message = "Obese" 
    end 
  end
end
```