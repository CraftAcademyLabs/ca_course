# Prototypes & Classes

###JavaScript Prototypes
All JavaScript objects inherit the properties and methods from their prototype.

Objects created using an object literal, or with new `Object()`, inherit from a prototype called `Object.prototype`.

Objects created with new `Date()` inherit the `Date.prototype`.

The `Object.prototype` is on the top of the prototype chain.

All JavaScript objects (`Date, Array, RegExp, Function`, ....) inherit from the `Object.prototype`.

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