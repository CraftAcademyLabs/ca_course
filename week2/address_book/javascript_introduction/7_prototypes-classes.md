### JavaScript Prototypes

All JavaScript objects inherit the properties and methods from their prototype (`Date, Array, RegExp, Function`, ....).

We  use `.prototype` to  group methods together, not unlike a Ruby class.

### Javascript "Classes"

Javascript (ES5) doesn’t have a formal class notation, but you can create a “constructor” function and add methods to it. Examples from here.

```javascript
function Person(first, last) { 
    // create "constructor"
    this.first = first;        
    // public variables -- reference current object
    this.last = last;

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

## Javascript Exercise #2

1.  What is `this`? Does it have an equivalent in Ruby?
2.  The above Person class is written with ES5, now that we've learned about ES2015, convert the above code class to an ES2015 version using the new class syntax


<iframe height="400px" width="100%" src="https://repl.it/@diraulo/classes-es6?lite=true" scrolling="no" frameborder="no" allowtransparency="true" allowfullscreen="true" sandbox="allow-forms allow-pointer-lock allow-popups allow-same-origin allow-scripts allow-modals"></iframe>