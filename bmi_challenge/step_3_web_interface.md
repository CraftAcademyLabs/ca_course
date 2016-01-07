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





