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