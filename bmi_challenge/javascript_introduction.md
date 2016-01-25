## JavaScript introduction

###Enabling JavaScript
Include javascript inside HTML:
```html
<script>
  x = 3;
</script>
```
Reference external file:
```html
<script src="http://example.com/script.js"></script>
```
Not all browsers are JavaScript enabled. You might want to be prepared for that and redirect the user to a different page if JavaScript is disabled.
```html
<noscript>
    <meta http-equiv="refresh" content="0; URL=http://example.com/noscript.html"/>
</noscript>
```
###Variables, Objects and Arrays
Open the console in your browser (Chrome) and try out this commands
```javascript
var string = "Hello";                
    // local variable, when inside a function
    
string_2 = "Hello World";             
    // global variable in default context (window.string_2)

string_3 = 'My string can include quotes: "Yeah" ';       
    // single or double quote

string_4 = "My really really really \
really long string broken into \
multiple lines";
```
```javascript
num = 19;                         
    // note lack of no ""
    
num = 0xfe + 2.343 + 2.5e3;       
    // hex, floats, exp

var newObject = new Object();     
    // constructor
    
newObject = {};           
    // constructor shorthand
    
newObject.name = "Thomas"            
    // dynamic attributes
    
newObject.name = null         
    // it's there (null item)
    
delete newObject.name         
    // it's gone (undefined)
    
newObject["real age"] = 33;       
    // array notation/hash table

var person = {           
    name: "Thomas",             
    details: {
        age: 44,
        "favorite color": "orange"
    }
}
    // create object using JSON (Javascript Object Notation)
    
person.name

person.details["favorite color"]

var newArray = [];                // no size
newArray[3] = "hi";               // grows dynamically
newArray[2] = 13;                 // any type
newArray.push(newObject);         // add new item
newArray.pop();               // remove it
```
Comparisons and Manipulations
Javascript has some funky types and comparisons.

/* javascript types */
typeof("string") == "string"
typeof(3) == typeof(3.4) == typeof(0x34) == "number"
typeof(myObject) == typeof(myArray) == "object" // arrays are objects
typeof(true) == typeof(1 == 2) == "boolean"
typeof(Math.sin) == "function"
typeof(notthere) == "undefined"

/* comparisons */
123 == "123"                     // true => converts type
123 === "123"                    // false => checks type
typeof(x) == "undefined"     // x isn't there
x == null            // x is defined, but null

/* Numbers */
parseInt("123")          // base 10 => 123
parseInt("123", 16);         // base 16 => 291
parseFloat("123.43");        // 123.43

isNaN(0/0) == true       // illegal number
3/0 == Infinity          // legal...
-3/0 == -Infinity        //
isFinite(3/0) == false       // ... but not finite

/* regular expression (regex) string comparisons */
matches = "hello".match(/h../)   // returns array ["hel"] or null

re = new RegExp("h..", "ig");    // construct regexp -- no slashes
matches = "hello".match(re);     // use it

"hello".replace(/h/,"b")     // => "bello"
Conditionals and Loops
if (str == "Hello"){    // if-else
  alert("Hi");      // popup dialog
}
else{
  alert("something is wrong!");
}

a = 3, b = 4;       // multi-assigment
c = a > b ? a : b;  // c gets bigger item (b)

switch (name){      // switch statement
  case "Bob":
    alert("Hi Bob!")
    break
  case "Joe":
    alert("Hey Joe.")
    break
  default: alert("Do I know you?")
}

while (i < n){          // the basics
 // do something
 i++;
}

for (var i=0; i<n; i++){
  // do something else
}

for (var key in obj){
  // do something with obj[key]
}
Defining Functions
function foo(a,b){          // global function
  return a + b;
}

var fn = function(a,b){     // save function as variable...
  return foo(a,b);
}

obj.fn = function(a,b){     // ...or as part of object
  return a + b;
}

function bar(a,b){
    var n = 1;                  // local var

    function helper(x) {            // inner function...
        return 1/Math.sqrt(x + n);  // .. can use local vars
    }
    return helper(input);           // avoid need for global function
}

foo(1,2) == fn(1,2) == 3;   // true
Javascript Classes
Javascript doesn’t have formal class notation, but you can create a “constructor” and add methods to it. Examples from here.

function Person(first, last) { // create "constructor"
    this.first = first;        // public variables -- reference current object
    this.last = last;

    var privateFn = function(first, last){  // private function
    // ...
    }

    this.setName = function(first, last){ // public function
        this.first = first;
    this.last = last;
    }

}

Person.prototype.fullName = function() { // extend prototype
    return this.first + ' ' + this.last; //   even at runtime!
}

var bob = new Person("Bob", "Smith"); // "new" creates an object
bob.fullName();               // => "Bob Smith"
Advanced Javascript
function foo(a,b){        // will raise exception
  var c = a + b;
  throw "Error Message"; // your message here
}

try{                     // try dangerous code
   foo(1,2);
}
catch(e){           // catch exception
   alert(e.name + e.message);   // show details
}

eval("x = 3");       // execute arbitrary code

timer = setTimeout("myfunction()", 1000)  // execute in 1 second (1000ms)
clearTimeout(timer);              // cancel event
Browser Document Object Model (DOM), and GUI
Find and change HTML elements.

alert("message");  // messagebox with "OK"
var choice = confirm("message");  // OK/CANCEL true or false
var input = prompt("message", "default value"); // enter a value; null if cancelled

x = document.getElementById("foo");    // finds <div id="foo"></div>

x.style.background = "#333";           // set CSS style
x.style.borderLeft = "1px solid #ccc"; // border-left => borderLeft (camelCase)

x.className = "myclass";           // set CSS class
x.innerHTML = "Hello";             // set html inside div

y = document.getElementById("myinput"); // input area/textarea
y.value = "Hi";                 // get or set text


