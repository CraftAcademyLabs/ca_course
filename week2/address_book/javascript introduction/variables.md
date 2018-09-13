# const and let vs var
`const` stands for "constant" as is used when the variable that you are declaring is not to be changed.
`let` is used when the varible that you are declaring may or not be changed.
`var` is the old syntax, and you may see it in the future, var is a very loose variable declaration and can be changed later in the code.

Using `let` and `const` gives us a heads-up when reading code so that we will know what will happen with the variable later on in code and forces us to think about how and where we are declaring variables.

Let's see some examples:


<p><iframe height="400px" width="100%" src="https://repl.it/@diraulo/variable-declarations?lite=true" scrolling="no" frameborder="no" allowtransparency="true" allowfullscreen="true" sandbox="allow-forms allow-pointer-lock allow-popups allow-same-origin allow-scripts allow-modals"></iframe></p>



Try adding `const PI = 100` on a new line after `const PI = 3.14` and see what happens

# Arrow functions in JS

First, we must understand that JavaScript distinguishes expressions and statements.

- An expression produces a value and can be written wherever a value is expected, for example as an argument in a function call.

- A statement performs an action

**Different ways of declaring arrow functions:**

Defining an arrow function:

```javascript
(param1, param2) => { statements }
```

When we don't use curly bracket the arrow function has an implicit return meaning it will return the value
```javascript
(param1, param2) => expression
```
This way of writing arrow functions is the same as the one above, but when we write an arrow function with curly brackets then we need to have the return keyword.
```javascript
(param1, param2) => { return expression; }
```

If we are writing arrow functions without parameters then we need to have brackets to define the function.
```javascript
() => { statments }
() => expression
() => { return expression; }
```

<iframe height="500px" width="100%" src="https://repl.it/@diraulo/functions-declaration?lite=true" scrolling="no" frameborder="no" allowtransparency="true" allowfullscreen="true" sandbox="allow-forms allow-pointer-lock allow-popups allow-same-origin allow-scripts allow-modals"></iframe>