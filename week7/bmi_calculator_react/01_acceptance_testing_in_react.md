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

## Your first tests

Now it is time that we write our first test, which is going to be the scenario of calculating the BMI. The test that you are now going to see has the imperial method already implemented. It could be that you have also implemented the imperial method but in a different way because we gave you free hands with that feature.

This is just our example that works with our implementation. We encourage you to update this test so it works for your implementation.

Run:

`$ touch cypress/integration/userCanCalculateTheirBMI.spec.js`

Read through the code (and comments) below and add it to the feature file you just created:
```
describe('BMI Converter', () => {
    before(function() {
        cy.visit('http://localhost:3000');
    });

    beforeEach(function() {
        // Reload the application between tests to reset state
        cy.reload();
    });

    it('should display "BMI Converter" text on page', () => {
        cy.contains('BMI Converter');
    });

    describe('Metric method', () => {
        beforeEach(() => {
            // This before block will be executed prior to each test in this describe block
            cy.get('select[id="method"]').select('metric')
            cy.get('input[name="weight"]').type('95')
            cy.get('input[name="height"]').type('186')
        })

        it('displays assesment', async () => {   
            cy.contains('You are Overweight')
        })

        it('displays BMI value', async () => {   
            cy.contains('BMI of 27.46')
        })
    })

    describe('Imperial method', async () => {
        beforeEach( async () => {
            // This before block will be executed prior to each test in this describe block
            cy.get('select[id="method"]').select('imperial')
            cy.get('input[name="weight"]').type('200')
            cy.get('input[name="height"]').type('73')
        })

        it('displays assesment', async () => {   
            cy.contains('You are Overweight')
        })

        it('displays BMI value', async () => {   
            cy.contains('BMI of 26.38')
        })
    })
}); 
```
The important part that you need to understand is what is happening in the `beforeEach` and `before` blocks. The `before` makes sure that the test browser goes to the correct URL to run the tests. The `beforeEach` resets the page (and the input fields) after every test.

The testing of the actual method may change depending on the implementation that you have. As you can see it is pretty straight forward, we select the method from a selector, fill in the inputs with the relevant information and then expect what response is going to be shown on the page.

If you need to press a button or interact with the page in some different way then what we show you above, you can go and find that in the [Cypress documentation](https://docs.cypress.io/guides/overview/why-cypress.html#In-a-nutshell).

## Wrap up

Acceptance tests help you in making sure that your application does what it is supposed to do when a user interacts with it. It requires limited knowledge about the implementation itself (how each component works). Acceptance tests are just one of the testing strategies we will use when developing our React applications.