# Weekend Challenge

## Fizzbuzz.js



In this challenge we are revisiting the fizzbuzz kata, but in JS!



We are also bringing back the training wheel testing package to write test to our code. We have intentionally left some syntax errors in the code for you to find.

As the final part of the challenge you need to deploy your website using github pages.

Learning objectives

- Ability to explain concepts
- Practice JS
- Combine both feature and unit test and understand their difference
- Read and understand documentation
- Debug and find errors
- Find and remove unneccesary code and files.


**What we are expecting to see in your implementation:**


- A deployed website with some styling

- All test are going green

- Good and readable commits messages, commits made after every test going green or when you have added implementation code

- Usage of branches

- A PR towards your own master branch

- Answers to all of the questions in the README

- Markdown syntax used in the README with proper code formatting




### Setup

1. Create a folder called:

```bash

$ mkdir fizzbuzz_js_yourname

```


2. Move into that folder and initialize git:

```bash

$ git init

```

3. Initialize npm with:

```bash

$ npm init

```

4. Create a README


5. Create a repo on GitHub and connect it to your local machine.


6. Create and switch to a new branch called `development`,

this will be the branch that you are going to work from.


7. Go over to the training wheels [package](https://www.npmjs.com/package/e2e_training_wheels) and follow the instructions to install it. This is the same package that we used in the BMI-challenge. Install the package using the automatic setup. You will get some scary messages when you run the `npm link` command, ignore those and run `npm run test`. You should get passing tests if everything is set up properly.


**Make sure that you create a `.gitignore` and add `node_modules` before you commit**

8. We are going to unit test so we need a spec folder which is created when installing e2e-testing package


Add the following code to the `spec.helper.js`

```js
const fs = require('fs');
let fizzBuzz = fs.readFileSync('./src/js/fizz-buzz.js');
eval( fizzBuzz + `\nexports.FizzBuzz = FizzBuzz;`)
```

---

***Question 1*. In your README to the best of your knowledge please explain what the following lines of code do**

```js
let  fizzBuzz = fs.readFileSync('./src/js/fizz-buzz.js');
eval( fizzBuzz + `\nexports.FizzBuzz = FizzBuzz;`)
```
---

We are now done with the setup let's move to the next part.



------



## Unit testing

  

Create a file called `fizz-buzz.spec.js`
```bash
$ touch spec/fizz-buzz.spec.js
```

Add  
```js
const { FizzBuzz } =  require('../spec.helper')
```
to the top of the `fizz-buzz.spec.js`

Add also your `describe` block to the same file
```js
describe('Fizz Buzz', () => {
	let fizzBuzz = new FizzBuzz
})
```

Add your first test to the `describe` block
```js
it('returns a number if no game rules are met', () => {
	expect(fizzBuzz.check(1)).to.eql(1)
})
```
Run `npm run specs` after to make sure that you have the right setup

----
**Question 2*. In your README to the best of your knowledge please explain why we are placing the**
 ```js
let fizzBuzz = new FizzBuzz
```
**outside the `it` block?**

---

Now it's time to add implemenation code.
Create a `fizz-buzz.js` file to keep our code.

```bash
$ touch src/js/fizz-buzz.js
```

Run `npm run specs` you should get the following error message:
`ReferenceError: FizzBuzz is not defined` make sure that you scroll through the entire output  to find that error message. 

Add the following code to the `fizz-buzz.js` file:
```js
function FizzBuzz()  {
	this.check = (number) => {
		return number:
	}
}
```
Run `npm run specs` you should have the test going green. If it does commit and move on to the next test. 

Add a new it block to fizz-buzz.spec.js
 ```js
 it('returns Fizz if number is divisible by 3', () => {
	expect(fizzBuzz.check(3)).to.eql('Fizz')
})
 ```
 Run `npm run specs` And read the error message
 
Add the following implementation code:
```js
function FizzBuzz() {
	this.check = (number) => {
		if (number % 3 === 0) {
			return 'Fizz';
		} else {
			return number
		}
	}
}
```
Run `npm run specs` you should have the test going green. If it does commit and move on to the next test. 

----
***Question 3*. In your README to the best of your knowledge please explain the difference between using `===` and `==` in JS?** 

---


Lets do the same procedure add the new test
 ```js
 it('returns Buzz if number is divisible by 5', () => {
	expect(fizzBuzz.check(5)).to.eql('Buzz')
})
 ```
Run `npm run specs` And read the error message

Add the implementation code:
```js
function FizzBuzz() {
	this.check = (number) => {
		if (number % 5 === 0) {
			return 'Buzz';
		} else if (number % 3 === 0) {
			return 'Fizz';
		} else {
			return number
		}
	}
}
```

Run `npm run specs` you should have the test going green. If it does commit and move on to the next test.


----
***Question 4*. In your README to the best of your knowledge please explain why we are moving `(number % 5 === 0)` to the top?**

---

Now for the last time add the test:
 ```js
 it('returns FizzBuzz if number is divisible by 15', () => {
	expect(fizzBuzz.check(15)).to.eql('FizzBuzz')
})
 ```

  Run `npm run specs` And read the error message

And add the implementation code:

```js
function FizzBuzz() {
	this.check = (number) => {
		if (number % 5 === 0) {
			return 'FizzBuzz';
		} else if (number % 5 === 0) {
			return 'Buzz';
		} else if (number % 3 === 0) {
			return 'Fizz';
		} else {
			return number
		}
	}
}
```

Run `npm run specs` you should have the test going green. If it does commit and lets move to the next section.


## Feature testing



----
***Question 5*. In your README to the best of your knowledge please explain the difference between feature and unit test**

---


We are now moving over to feature testing. We should have working logic by now. So let's make sure that we can visably see the results.

Add a proper description on what we want to test for to the `application.feature.js` file

----
***Question 6*. In your README to the best of your knowledge please explain what this following code does**

```js
describe('User can input a value and get FizzBuzz results', () => {
	before(async () => {
		await  browser.init()
		await  browser.visitPage('http://localhost:8080/')
	});

	beforeEach(async () => {
		await  browser.page.reload();
	})

	after(async ()=> {
		await  browser.close();
	})
})
```
---


Run `npm run test` and make sure that our setup is correct.

Now lets add the `it` block to our `application.feature.js` file, replace the autogenerated `it` block

```js
it('clicking on the "Check" button', async () => {
	await browser.fillIn("input[id='value']", { with:  "3" })
	await browser.clickOnButton("input[value='Check']")
	let content = await browser.getContent("[id='display_answer']")
	expect(content).to.eql('Fizz');
})
```

----
***Question 7*. In your README to the best of your knowledge please explain what expectations in the context of testing are**

---

Run `npm run test`, you should get the error message:
```
1) 'clicking on the "Check" button'

[...]

TypeError: Cannot read property 'type' of null
```

Let's fix this


If we think about it we know what we need to add to the `index.html` file

We will need:
- A input field for us to enter the number
- A button to start the calculation
- A `<div>` where we can display the result

Lets add those in the `<body>` tag

```html
<input type="text" id="value">
<input type="submit" value="Check" id="button">
<div id="display_answer"></div>
```

In order to call on the connect our javascript code with the DOM elements we need some code to execute that action. Add the following code in the bottom of the file. Just make sure that the scripts are inside the `<html>` tag.

```html
<script src="./js/fizz-buzz.js"></script>
    <script>
        document.addEventListener('DOMContentLoaded', () => {
            let button = document.getElementById('button')
            let displayDiv = document.getElementById('display_answer')
            button.addEventListener('click', () =>{
                let value = document.getElementById('value').value
                let fizzBuzz = new FizzBuzz
                let result = fizzBuzz.check(value)
                displayDiv.innerHTML = result;
            })
        })
    </script>
```

Run the tests again and make sure that everything is going green.

----
***Question 8*. In your README to the best of your knowledge please write a line to line explanation of what is happening in this code**

```html
<script src="./js/fizz-buzz.js"></script>
    <script>
        document.addEventListener('DOMContentLoaded', () => {
            let button = document.getElementById('button')
            let displayDiv = document.getElementById('display_answer')
            button.addEventListener('click', () =>{
                let value = document.getElementById('value').value
                let fizzBuzz = new FizzBuzz
                let result = fizzBuzz.check(value)
                displayDiv.innerHTML = result;
            })
        })
    </script>
```

---


### Styling

Now it´s time to add some styling. Make use of the Tailwind CSS framework to minimally style your application.

You need to add Tailwind through a CDN

Run your test after you have styled the webpage in order to make sure that the code still works.

----
***Question 9*. In your README to the best of your knowledge please explain what a CDN (Content Delivery Network) is?**

---

### Deployment

Now it's time to deploy the application.
We will make use of GitHub-pages to do so. However GitHub only recognizes `index.html` that are in the root folder.

This means that we need to modify our configuration of our feature test so we can see that the test are run.

Go to the package.json and only remove `src` from the following line:
```js
"features": "superstatic src -p 8080 & mocha --timeout 100000 --recursive  --reporter=spec features PORT=8080 npm run stop-test-server ",
```

run your tests again they will fail. Add the proper path for `fizz-buzz.js` in the index html and make sure that the tests are not failing.

Commit, push and deploy the site.

### PR

Create a PR towards your master branch, but do not merge it.
Submit the link to the PR in the submission section.

### Extras
When you are done you can continue working on your website.

You can:
- Refactor your fizzbuzz code with some ES6
- Style your site some more
- Extract the script with the eventlistners from index.html to a separate file

