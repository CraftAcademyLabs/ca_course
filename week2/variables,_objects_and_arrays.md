---
title: "Variables, objects and arrays"
subtitle: "Week 2"
author: [Craft Academy - Coding as a Craft]
date: Version 0.1
subject: "Javascript"
keywords: [Javascript]
titlepage: true
titlepage-color: f28e24
titlepage-text-color: "FFFFFF"
titlepage-rule-color: "FFFFFF"
titlepage-rule-height: 2
...

# Variables, objects and arrays

Open the console in your browser (Chrome) and try out these commands:

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

###Numbers

```javascript
num = 19;                         
    // note lack of ""

num = 0xfe + 2.343 + 2.5e3;       
    // hex, floats, exponents
```

###Objects

```javascript
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
    // note the difference between age: and "favorite color" - one is a symbol and one is a string. They are accessed differently.

person.name
    // "Thomas"

person.details["favorite color"]
    // "orange"

person.details.age
    // 44
```

###Arrays

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