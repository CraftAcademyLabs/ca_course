## FizzBuzz Challenge
### Week 2 JavaScript/Jasmine challenge
**Do you remember the Fizz Buzz game we ware working on in the Prep-course? At that time we wrote the program using Ruby. Let's do it in JavaScript and implement a web interface using HTML and jQuery. To successfully complete the challenge, I want you to deploy it using GitHub pages (also something we've done during the Prep-course, remember?)**

We have prepared this repository for you with all the necessary tools in terms of a testing framework, the  basic folder structure, some spec files, etc.  What you need to do it to make it all work together.

### Instructions
Read this entire README carefully and follow all instructions.
- Challenge time: this weekend, until Monday 9.30am
- Feel free to use Google, Stack Overflow, your notes, previously written code, books, etc. but work on your own
- If you refer to or have in whole or partially used the solution of another coach or student, please put a link to that in your README
- If you have a partial solution, **still check in a partial solution to GitHub and create a Pull Request**
- You must submit a Pull Request to this repository with your code by 9.30am Monday morning - before the stand-up
- **NOTE! Place all your production code in the `src` folder (html files directly in that folder and JavaScript files in `src/js`)**
- **Make sure that you have a close look at the `Gemfile` to see what libraries will be used. That will have an impact on how you will be running your tests.**
- **Make sure you read the documentation for the gems used.**

### Learning objective
 
--------------------------------------------------------------------------------

- To write Unit tests with Jasmine
- To write Acceptance tests with Jasmine-jQuery
- To add HTML elements and get user data
- To modify the DOM using jQuery
- To deploy your code using GH-Pages


For the web interface there is this User story

```
As an individual
So that I can check a number when playing FizzBuzz
I would like to be able to enter a number in an field, click a button and see
the output according to the FizzBuzz rules.
```

### Tasks

-------------------------------------------------------------------------------
#### TODO:

- Fork the challenge repo: [https://github.com/CraftAcademy/fizz_buzz_js_challenge](https://github.com/CraftAcademy/fizz_buzz_js_challenge)
- Run the command 'bundle' in the project directory to ensure you have all the gems
- Write your specs and implementation
- Be smart about using Git: commit and push often. Use feature branches.
- Create a Pull Request as soon as possible

#### Review and reflect
Compare this implementation to the Ruby version we wrote a while back during the prep-course. 

1. What are the similarities/differences between the Ruby and js versions?
2. How do you find unit testing with Jasmine in comparison to RSpec? 
3. How can you "gamify" Fizz Buzz? Consider the User Interface/Experience. Can the game be played by multiple players? What about mobile devices? 


### What we are looking for

--------------------------------------------------------------------------------

#### I'm hoping to see that:
- You can use the Jasmine testing framework for both Unit as well as Acceptance tests.
- You understand how to modify DOM elements using jQuery.
- I can track your work by following you commit history - so please commit as soon you are done with a feature or when you have made a test pass.

#### In your Pull Request, I'm hoping to see:
- That you are testing the right thing in the right spec file.
- That all tests passing - green is good!
- High test coverage (above 95% is accepted)
- The code is easy to follow: specs are well described, variable names are easy to understand, functions are short, code is nicely formatted, etc.
- The README.md includes relevant information about your solution

**Happy coding!**

Here is the Fizz Buzz game in JavaScript:

```js
function FizzBuzz() {
};

FizzBuzz.prototype._hasZeroReminder = function (number, divisor) {
    return (number % divisor === 0);
};

FizzBuzz.prototype.divisibleByThree = function (number) {
    return this._hasZeroReminder(number, 3);
};

FizzBuzz.prototype.divisibleByFive = function (number) {
    return this._hasZeroReminder(number, 5);
};

FizzBuzz.prototype.divisibleByFifteen = function (number) {
    return this._hasZeroReminder(number, 15);
};

FizzBuzz.prototype.fizz_buzz = function (number) {
    if (this.divisibleByFifteen(number)) {
        return "FizzBuzz";
    }
    else if (this.divisibleByFive(number)) {
        return "Buzz";
    }
    else if (this.divisibleByThree(number)) {
        return "Fizz";
    }
    else
        return number;
};
```
