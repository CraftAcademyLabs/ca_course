# Defining Functions

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
        // defining a function inside of another function
        return 1/Math.sqrt(x + n);  
        // can use local vars
    }
    return helper(b);           
        // avoid need for global function
}

foo(1,2) == fn(1,2)   
    // true (3)
bar(1,3);
    // 0.5
```
##Javascript Exercises #2
1. Write a function that returns your first name. Call it.
2. Write a new function that takes your name as an input. The function should return your first name, plus your last name, as one string. (Hint: strings can be combined with a `+`)