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
### Numbers
```javascript
num = 19;                         
    // note lack of ""
    
num = 0xfe + 2.343 + 2.5e3;       
    // hex, floats, exp

var newObject = new Object();     
    // constructor
```
### Objects
```javascript
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
    // "Thomas"

person.details["favorite color"]
    // "ornage"

person.details.age
    // 44
```
### Arrays
```javascript
var newArray = [];                
    // empty array
    
newArray[3] = "hello";               
    // grows dynamically
    
newArray[2] = 13;                 
    // add any datatype
    
newArray.push(newObject);         
    // add new item at last index
    
newArray.pop();               
    // remove it from last index
```
### Comparisons and Manipulations
JavaScript has some 'weird' datatypes and comparisons.

#### Datatypes
```javascript
typeof("text") == "string"
    // true
    
typeof(3) == typeof(3.4) == typeof(0x34) == "number"
    // true

typeof(myObject) == typeof(myArray) == "object" 
    // true (arrays are objects)
    
typeof(true) == typeof(1 == 2) == "boolean"
    // true
    
typeof(Math.sin) == "function"
    // true
    
typeof(notThere) == "undefined"
    // true (can be useful)
```

#### Comparisons
```javascript
123 == "123"                     
    // true (converts type)
    
123 === "123"                    
    // false (checks type)
    
typeof(x) == "undefined"     
    // true (x isn't there)
x == null            
    // x is not defined
```
####Numbers
```javascript
parseInt("123")          
    // base 10 => 123
    
parseInt("123", 16);         
    // base 16 => 291
    
parseFloat("123.43");        
    // 123.43

isNaN(0/0) == true       
    // illegal number
    
3/0 == Infinity          
    // true (Infinity is displayed when a number exceeds the upper limit of the floating point numbers, which is 1.797693134862315E+308)
    
-3/0 == -Infinity        
    // true (-Infinity is displayed when a number exceeds the lower limit of the floating point numbers, which is -1.797693134862316E+308)

isFinite(3/0) == true       
    // false (The isFinite() function determines whether a number is a finite, legal number. This function returns false if the value is +infinity, -infinity, or NaN (Not-a-Number), otherwise it returns true.)
```
####Regular expression (regex) string comparisons
```javascript
matches = "hello".match(/h../)   
    // returns array ["hel"] or null

myRegex = new RegExp("h..", "ig");    
    // construct regexp -- no slashes
    
matches = "hello".match(myRegex);     
    // use the regex

"hello".replace(/h/,"b")     
// => "bello"
```
####Conditionals and Loops
```javascript
if (string == "Hello"){    
  alert("Hi");       
}
else{
  alert("something is wrong!");
}
    // if-else popup dialog

a = 3, b = 4;       
    // multi-assigment
    
c = a > b ? a : b;  
    // c gets bigger item (b) (this is a ternary operator)

switch (name){      
  case "Thomas":
    alert("Hi Thomes!")
    break
  case "John":
    alert("Hi John.")
    break
  default: alert("Who are you?")
}
// switch statement

while (i <= n){ 
    console.log(i);        
    i++;
}
    // do something untill a value is reached

for (var i=0; i<=n; i++){
    console.log(i);
}
    // another way to do something 

for (var key in person){
    console.log(key)
}
    // do something with person[key]
```
###Defining Functions
```javascript
function foo(a,b){          
  return a + b;
}
    // global function

var fn = function(a,b){     
  return foo(a,b);
}
    // save function as a variable

person.fn = function(a,b){     
  return a + b;
}
    // or as part of object

function bar(a,b){
    var n = a;                  
        // local var
    function helper(x) {            
        // inner function...
        return 1/Math.sqrt(x + n);  
        // can use local vars
    }
    return helper(b);           
        // avoid need for global function
}

foo(1,2) == fn(1,2) == 3;   
    // true
bar(1,3);
    // 0.5
```
###Javascript Classes
Javascript doesn’t have formal class notation, but you can create a “constructor” and add methods to it. Examples from here.
```javascript
function Person(first, last) { 
    // create "constructor"
    this.first = first;        
    // public variables -- reference current object
    this.last = last;

    var privateFn = function(first, last){  
    // private function
    }

    this.setName = function(first, last){ 
        // public function
        this.first = first;
        this.last = last;
    }

}

Person.prototype.fullName = function() { 
    // extend prototype
    return this.first + ' ' + this.last; 
    // even at runtime!
}

var bob = new Person("Thomas", "Ochman"); 
    // "new" creates an object
bob.fullName();               
    // "Thomas Ochman"
```
#### Miscelenious
```javascript

eval("x = 3");       // execute arbitrary code

timer = setTimeout("myfunction()", 1000)  
    // execute in 1 second (1000ms)
    
clearTimeout(timer);             
    // cancel event
```
###Document Object Model (DOM)
Find and change HTML elements.
```javascript
alert("message");  
    // messagebox with "OK"
    
var choice = confirm("message");  
    // OK/CANCEL true or false
    
var input = prompt("message", "default value"); 
    // enter a value; null if cancelled

x = document.getElementById("foo");    
    // finds element by id

x.style.background = "#333";           
    // set CSS style
    
x.style.borderLeft = "1px solid #ccc"; 
    // border-left => borderLeft (camelCase)

x.className = "myclass";           
    // set CSS class
    
x.innerHTML = "Hello";             
    // set html inside div

y = document.getElementsByClassName("my-class"); 
    // find element by class

```
