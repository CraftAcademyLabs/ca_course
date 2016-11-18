# Prototypes & Classes

###JavaScript Prototypes
All JavaScript objects inherit the properties and methods from their prototype (`Date, Array, RegExp, Function`, ....).

We use `.prototype` to group methods together, not unlike a Ruby class.

###Javascript "Classes"

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
##Javascript Exercise #3
1. What is `this`? Does it have an equivalent in Ruby?