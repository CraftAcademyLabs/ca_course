# Javascript Introduction

This week you'll learn a completely new programming language: Javascript. While many developers use Javascript for all three layers of an application (data/persistence, logic, and presentation), we'll be using Ruby for logic and data and often both Ruby and Javascript elements for presentation.

Javascript and Ruby (and most programming languages) have a lot in common. They both have variables, functions, arrays, and hashes. And thousands of other matching structures. But in many cases these are used slightly differently amongst languages. For instance, in Ruby a variable is declared like so:

```ruby
age = 32
```
In Javascript:
```javascript
var age = 32;
```
Functions look very different. In Ruby:
```ruby
def foo(a, b)
  a + b
end
```
In Javascript we'll typically want to store a function inside a variable:
```javascript
var fn = function foo(a,b){
  return a + b;
}
```

The following pages will give you a huge list of Javascript variables to play with. Open up any browser (we suggest Chrome), click "Inspect" and click "Console". Then you can put in any Javascript code - just like in IRB in the terminal.

###A few notes about Javascript
- As we said above, it runs in the browser and not in the terminal. That may not mean much to you now, but keep it in mind.
- Debugging does not take place with `pry` but with `debugger;`. It works in a similar way, pausing the code. You don't have to install anything.
- Javascript is very unforgiving. In Ruby you can skip spaces, parentheses, etc. If you forget an `end` it will probably tell you `expected tEND`. Javascript isn't like that. You have to be very careful about your commas, colons, parentheses, curly brackets, and everything else. Every line in Javascript should end with a `;`.


