## Acceptance testing

We are going to use [Cypress](https://www.cypress.io/) to write our acceptance test for this application.

## Installing Cypress

**Step1**: You need to run:

`npm i cypress --save-dev`

OR

`yarn add cypress --dev`

That's it. No, really! we are done here.

## Set Up Cypress

Once we are done with the installation, we can launch cypress by

`./node_modules/.bin/cypress open`

But that's far too long, so we create a shortcut in our `package.json` by inserting `"cy:open": "yarn start --silent & cypress open"` in `scripts`.

If you use npm, the script should look like this `"cy:open": "npm start --silent & cypress open"`

Then we can open cypress by `npm run cy:open`

Once we run cypress for first time, it will create scaffolded folder, with a lot of example tests (**Have a look at them, to get a sense of how tests should be written**).

Our tests are stored in `Integration` folder and `fixtures` folder will contain JSON files, which we will introduce during the course of this document.

Configure our baseUrl in `cypress.json` file:

```json
// cypress.json
{
  "baseUrl": "http://localhost:3000"
}
```

## Your first tests

Now it is time that we write our first test, which is going to be the scenario of calculating the BMI. The test that you are now going to see has the imperial method already implemented. It could be that you have also implemented the imperial method but in a different way because we gave you free hands with that feature.

This is just our example that works with our implementation. We encourage you to update this test so it works for your implementation.

Run:

`$ touch cypress/integration/userCanCalculateTheirBMI.spec.js`

Read through the code (and comments) below and add it to the feature file you just created:
```js
// cypress/integration/examples/BMI.spec.js

/// <reference types="Cypress" />

describe('BMI Calculator', () => {
  it('Calculates BMI in metric',() => {
    cy.visit('/');
    cy.get('input#weight').type(90);
    cy.get('input#height').type(190);
    cy.get('button#calculate').click();
    cy.get('p#bmi-message').should('contain', 'You are Normal with a BMI of 24.93')
  })
})
```

This is a very simple test of our calculation, we just use our selectors to input and then press the button to see the message. 

If you need to press a button or interact with the page in some different way then what we show you above, you can go and find that in the [Cypress documentation](https://docs.cypress.io/guides/overview/why-cypress.html#In-a-nutshell).


## Imperial method

I will show you a test case for the imperial method. Your job will be to make the tests pass:

```js
// cypress/integration/examples/BMI.spec.js

/// <reference types="Cypress" />

describe('BMI Calculator', () => {
  it('Calculates BMI in metric',() => {
    cy.visit('/');
    cy.get('select#select-method').select('metric');
    cy.get('input#weight').type(90);
    cy.get('input#height').type(190);
    cy.get('button#calculate').click();
    cy.get('p#bmi-message').should('contain', 'You are Normal with a BMI of 24.93')
  })
  it('Calculates BMI in imperial',() => {
    cy.visit('/');
    cy.get('select#select-method').select('imperial');
    cy.get('input#weight').type(198);
    cy.get('input#height').type(74);
    cy.get('button#calculate').click();
    cy.get('p#bmi-message').should('contain', 'You are Overweight with a BMI of 25.42')
  })
})

```


## Wrap up

Acceptance tests help you in making sure that your application does what it is supposed to do when a user interacts with it. It requires limited knowledge about the implementation itself (how each component works). Acceptance tests are just one of the testing strategies we will use when developing our React applications.