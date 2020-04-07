## Back to our tests

Let's revisit our test for one moment. When we are dealing with stripe they provide us with a lot of functionality in terms of security and checks. This is good for development but could be an issue when we are trying to test the payment functionality. We need to test that we can see an iframe where we will be displaying the payment form. 

Refactor your test to look like this. 

```js
it("user can pay for his order", () => {
  cy.get("button")
    .contains("Confirm!")
    .click();
  cy.get("#payment-form").should("exist");
  cy.wait(1000);
  cy.get('iframe[name^="__privateStripeFrame5"]').then($iframe => {
    const $body = $iframe.contents().find("body");
    cy.wrap($body)
      .find('input[name="cardnumber"]')
      .type("4242424242424242", { delay: 50 });
  });
});
```

We are finding an input field and filling it out with the card number. Small steps remember. Run your test and behold the error messages. 

Time for the implementation

Add the following package. 

```bash
$ yarn add react-stripe-elements
```

To use stripe we also need to add a script provided by stripe.

Add a script to the head in index.html

```html
<script src="https://js.stripe.com/v3/"></script>
```

And we also need to turn off some of the built-in settings on cypress just to make sure that we are not getting errors because of our browser.

Setting chromeWebSecurity to false in Chrome-based browsers allows you to do the following:

- Display insecure content
- Navigate to any superdomain without cross-origin errors
- Access cross-origin iframes that are embedded in your application

Add `chromeWebSecurity` and set it to `false`

`cypress.json`

```json
{
  "baseUrl": "http://localhost:3001",
  "chromeWebSecurity": false
}
```
Head over to your `index.js` and import the StripeProvider. 

```js
import { StripeProvider } from "react-stripe-elements";
```

And wrap <App> component with the StripeProvider

```js
ReactDOM.render(
  <StripeProvider>
    <App />
  </StripeProvider>,
  document.getElementById("root")
);
```

The StripeProvider gives us access to the Stripe object. The Stripe object will contain the secret, and publishable key which will allow us to access the Stripe API. 

Get your API key from the stripe dashboard, make sure that you select "Show test data" and go to the developer's tab. Copy your Publishable key and add it to the stripe provider in your App component. 

```js
ReactDOM.render(
  <StripeProvider apiKey="put_key_here">
    <App />
  </StripeProvider>,
  document.getElementById("root")
);
```

Time to start working on the payment form itself.

