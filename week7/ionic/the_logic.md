# The logic - Step 1

Let's start at the unit level.

```
As an athlete,
In order to get to know my results from the Cooper test
I want to be able to input my distance and get a result from the normative data table
```

We will be doing all calculations on the client side and only save the results to our centralized database \(back-end\). This means that we will be working defining our logic in JavaScript. This means that we will be using Jasmine for Unit testing.

As a first step we want to make sure that our units are working.

Here are the steps you need to complete at this stage:

* Create a new project folder
* Set up Jasmine \([see this section](../bmi_challenge/jasmine_set_up.md)\).
* Write a JavaScript program that returns an assessment given the users gender, age and completed distance. Store that program in `cooper.js`
* Make the software accessible in your browsers console
* **You DON'T need to create a UI at this point.**

You need to create a `Person` object with a minimum of 2 attributes in order to be able to make the calculation - **gender** and **age** are required to be present.

```javascript
function Person(attr) {
  this.gender = attr.gender;
  this.age = attr.age;
};
```

Then, you need to add a function that takes the ** distance** of the 12-minute run and makes the calculation and fetches the results depending on **gender**.

There are several ways to achieve that. If you need some inspiration you can review [this repo](https://github.com/tochman/cooper_js_src) where you'll find two solutions. One using a `case` method and the second one that uses array index and ranges to set the message \(written by Raoul\).

Make sure you review the specs in order to understand how those method differ and how they can be implemented.

Remember, try to write your own implementation rather than just copying the provided code. It's all about learning. When the course is over, you'll be on your own solving problems.

**Once you are done, put that code aside. Even though it is the core of the application, we will not be using it for the next step - the back end. That part does not care about HOW we come up with the results, just THAT we do. The calculation will be used in our mobile app - not in the back-end.**

**UPDATE**: A TypeScript implementation of this logic is also available on [this repository](https://github.com/CraftAcademy/ts-cooper)

