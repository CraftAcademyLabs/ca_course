## Javascript Modules

Good authors divide their books into chapters and sections; good programmers divide their programs into modules

We can export modules from one file and import them into another file so we are able use them.

## Exporting in es6

```javascript
export default 'bottles'; // the module exports a default string
export function empty(){ } // exports named function empty
export const pi = 3.14 // exports a constant
export default class {} // exports a class
```

Whatever has been exported above can be imported into another file and used. Lets look at importing functions after which we can have a general discussion on export and import

## Importing the exported values

```javascript
// We can import the specific named exports above i.e empty and pi
import { empty, pi } from '..filepath/filename'
```

This creates variables `empty` and `pi` within the file scope \(remember variable scopes\) which means they can be used anywhere within the file e.g

```javascript
radius = 34;
area = pi * radius * radius; // this is possible since pi has been imported into this file
                            // lets not forget that pie was exported in a separate file
```

What if we exported the variable with a name \(possibly a vey long name\) but we want to use a different  
name in the import file? 

We use the `as` keyword for this to create an alias

```javascript
import { ThisVeryLongNameThatIdontLike as shortName } from '../filepath/filename'
 // inside this file you can now use the shortName
```

We can also import all the exported variables using the wildcard character then  
set an alias which will namespace the variables. Have a look at this

```javascript
import * as formBuilder from '../filepath/filename'; //imports every exported member from the file
console.log(formBuilder.pi) // console logs the pi value
console.log(formBuilder.empty)
//By namespacing we mean you go through that name (formBuilder) to get to the variable
```

## How this is used in angular and ionic

```javascript
//example of exports which exports a class that contains a function
export class CommentsPage {
    deleteComment() {console.log('deleted'}
}
```

Now to import

```javascript
// examples of imports - showing other imports
import { Component } from '@angular/core';
import { NavController, NavParams } from 'ionic-angular';
import { CommentsPage } from '../../providers/comments';
class exampleClass{
  console.log(CommentsPage.deleteComment()) // should output 'deleted'
}
```



