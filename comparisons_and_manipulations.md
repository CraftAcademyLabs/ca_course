# Comparisons and Manipulations

JavaScript has some 'weird' datatypes and comparisons.

###Datatypes

```javascript
typeof("text") == "string"
    // true

typeof(3) == typeof(3.4) && typeof(0x34) == "number"
    // true

typeof(myArray) == "object" 
    // true (arrays are objects)

typeof(true) == "boolean"
    // true

typeof(Math.sin) == "function"
    // true

typeof(notThere) == "undefined"
    // true (can be useful)
```

###Comparisons

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

###Numbers
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
###Regular expression (regex) string comparisons
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
###Conditionals and Loops
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
    // do something until a value (n) is reached
    // don't forget to have i++ or you will loop forever

for (var i=0; i<=n; i++){
    console.log(i);
}
    // another way to loop an n number of times

for (var key in person){
    console.log(key)
}
    // do something with person[key]
```

 