
## Intro to Stripe

This guide needs to have the order functionality from the previous chapter in place. We will continue on the code both from the backend as well as from the frontend. 

We are going to use stripe to finalize the order by paying for the products. 

Stripe is a payment gateway that is developer-friendly. 
In this section, we are going to set up the functionality that we need in order to make the payments with debit or credit card. 

First and foremost you need to create an account on stripe. We will get back to this later. But make sure that you have an account ready. So let's set the stage, create a new branch and let's get started. 

As with everything that we develop here at Craft Academy we work in a test-driven way. This means that we always start with our feature test to stay in scope but also make sure that we are not breaking any functionality that has already been added to the previous features. 

Start by creating a new feature by running the following command in your terminal.

`$ touch cypress/integration/userCanMakePayment.feature.js`

After the file has been created we need to add the following test. 

```js
describe("User can add a product to his/her order", () => {
  beforeEach(() => {
    cy.server();
    cy.route({
      method: "GET",
      url: "http://localhost:3000/api/products",
      response: "fixture:product_data.json"
    });

    cy.route({
      method: "POST",
      url: "http://localhost:3000/api/orders",
      response: "fixture:post_response.json"
    });

    cy.route({
      method: "PUT",
      url: "http://localhost:3000/api/orders/1",
      response: "fixture:put_response.json"
    });

    cy.visit("http://localhost:3001");
    cy.get("#product-2").within(() => {
      cy.get("button")
        .contains("Add to order")
        .click();
    });
    cy.get("#product-3").within(() => {
      cy.get("button")
        .contains("Add to order")
        .click();
    });
    cy.get("button")
      .contains("View order")
      .click();
  });

  it("user can pay for his order", () => {
    cy.get("button")
      .contains("Confirm!")
      .click();
    cy.get("#payment-form").should("exist");
  });
});
```
So what is happening in this test?
Well, we are setting the stage and making sure that we can display a payment form. Remember that we always work in small chunks and that we continually refactor both our test but also our implementation code as we add more and more functionality. 

Let's head over to the component that we want to display our payment form. 
