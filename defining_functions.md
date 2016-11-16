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