## Web interface

The next step is to create a web page that will allow for both input of data (create an instance of `Person`) and display the calculated results. 

For that we will be using HTML an jQuery. We will also make sure that we have automated tests for out interface so that we can be sure that everything behaves as we want it to behave.

### Testing jQuery
First, we need to make some adjustments to the Jasmine framework by extending Jasmine with Jasmine-jQuery: a set of jQuery helpers for Jasmine tests. 
 
Download/Copy Jasmine-jQuery from [this location](https://raw.githubusercontent.com/velesin/jasmine-jquery/master/lib/jasmine-jquery.js) and save to `lib/jasmine-jquery.js` 

In your `SpecRunner.html` locate where you include the Jasmine `boot.js`. Right underneath, make sure to include both `jasmine-jquery` and `jquery` (we will get thet from a remote location rather then including it in the file structure). 

```html
# SpecRunner-html

<script src='https://code.jquery.com/jquery-2.1.4.js'></script>
<!--script src="lib/jquery.min.js"></script-->
<script>
    $.holdReady(true);
</script>
<script src="lib/jasmine-jquery.js"></script>

```
Let's do a manual test to see if you have set everything up and jQuery has been loaded. Reload your `SpecRunner.html`, open up the console ond type in:
```js
typeof jQuery
```
If the response you get is `"function"` then you are all good and jQuery has been loaded. If you get `"undefined"` then something is wrong and you need to check your settings. 

In regard to the jQuery snippet `$ holdReady(true);` that we included in the `SpecRunner.html`, please see [this issue on GitHub](https://github.com/velesin/jasmine-jquery/issues/244) for an explanation.


Crete a new test file in the `spec` folder and call it `bmi_ui_spec.js`. Add the following code to that file.

```js
# spec/bmi_ui_spec.js

describe('BMI_UI - index.html', function() {
    beforeEach(function() {
        jasmine.getFixtures().fixturesPath = '.';
        loadFixtures('index.html');
        $.holdReady(false); 
    });

});
```

Create the `index.html` file in the main project folder and add a htm form that we need to input the data we need to create a `Person` and calculate the BMI. We also need to include jQuery (as we did in the `SpecRunner.html`) and make sure that the source files for `Person` and `BMICalculator` are loaded. 

**Go over the code below and make sure that you understand what is going on before you implement it.**

```html
# index.html

<!DOCTYPE html>
<html>
  <head>
    <meta charset="utf-8">
    <title></title>
    <script src="src/person.js"></script>
    <script src="src/bmi_calculator.js"></script>
    <script src='https://code.jquery.com/jquery-2.1.4.js'></script>
  </head>
  <body>
    <form>
      <input type="text" id="weight" placeholder="Weight">
      <input type="text" id="height" placeholder="Height">
      <input type="button" id="calculate" value="Calculate">
    </form>
    <div>
      <span id="display_value"></span>
      <span id="display_message"></span>

    </div>
  </body>
</html>

```

To open the web page go to your terminal and use the `open` command to fire up the page in the browser. 
```shell
$ open index.html
```

The page is still very static and nothing happens if you fill in values in the form and click `Calculate`. We need to add some jQuery code in order to tell the interface what to do. There are two ways to do that. We can eather create a new `js` file and include it in our code, or we can insert the script directly in the html. Let's do the latter. In order for HTML to understand that it is dealing with JavaScript, we need to wrap it in `<script></script>` tags. 

Have a look at the code below and once you understand what it does, add it to the `index.html` file.

```html
# index.html

<script type="text/javascript">
  $(document).ready(function () {
    $('#calculate').click(function () {
      var w = parseFloat($('#weight').val());
      var h = parseFloat($('#height').val());
      var person = new Person({weight: w, height: h});
      person.calculate_bmi();
      $('#display_value').html('Your BMI is ' + person.bmiValue);
      $('#display_message').html('and you are '+ person.bmiMessage);
    });
  });
</script>
```
Now you can play around with some values to see if everything is working properly. The next step will be to write some acceptance tests using Jasmine. In order to do that properly, we will introduce a web framework that we will be working with later during this course - [Sinatra](http://www.sinatrarb.com/).




