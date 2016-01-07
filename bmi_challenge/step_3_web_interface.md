## Web interface

The next step is to create a web page that will allow for both input of data (create an instance of `Person`) and display the calculated results. 

For that we will be using HTML an jQuery. We will also make sure that we have automated tests for out interface so that we can be sure that everything behaves as we want it to behave.

### Testing jQuery
First, we need to make some adjustments to the Jasmine framework by extending Jasmine with Jasmine-jQuery: a set of jQuery helpers for Jasmine tests. 
 
Download/Copy Jasmine-jQuery from [this location](https://raw.githubusercontent.com/velesin/jasmine-jquery/master/lib/jasmine-jquery.js) and save to `lib/jasmine-jquery.js` 



