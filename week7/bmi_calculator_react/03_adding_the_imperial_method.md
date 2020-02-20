## Imperial method

Now that you have finished the implementation of the BMI calculator, we would suggest that you try to implement the imperial method. Here we will provide you with how we would write a acceptance test for it.

The testing of the actual method may change depending on the implementation that you have. As you can see it is pretty straight forward, we select the method from a selector, fill in the inputs with the relevant information and then expect what response is going to be shown on the page.

```js
// cypress/integration/examples/BMI.spec.js

/// <reference types="Cypress" />

describe('BMI Calculator', () => {
  beforeEach(() => {
    cy.visit('/');
  })

  it('Calculates BMI in metric',() => {
    cy.get('select#select-method').select('metric');
    cy.get('input#weight').type(90);
    cy.get('input#height').type(190);
    cy.get('button#calculate').click();
    cy.get('p#bmi-message').should('contain', 'You are Normal with a BMI of 24.93')
  })
  it('Calculates BMI in imperial',() => {
    cy.get('select#select-method').select('imperial');
    cy.get('input#weight').type(198);
    cy.get('input#height').type(74);
    cy.get('button#calculate').click();
    cy.get('p#bmi-message').should('contain', 'You are Overweight with a BMI of 25.42')
  })
})

```
