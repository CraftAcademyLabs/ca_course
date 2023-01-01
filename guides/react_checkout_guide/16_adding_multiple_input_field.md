## Adding multiple input fields in the payment form

We need to update our test again for the remaining input fields, as well as with the assertion that the payment has gone through.

```js
it("user can pay for his order", () => {
  cy.route({
    method: "PUT",
    url: "http://localhost:3000/api/orders/1",
    body: { activity: "finalize" },
    response: {
      paid: true,
      message: "Your order will be ready in 30 minutes!",
    },
  });
  cy.get("button").contains("Confirm!").click();

  cy.get("#payment-form").should("exist");
  cy.wait(1000);
  cy.get('iframe[name^="__privateStripeFrame5"]').then(($iframe) => {
    const $body = $iframe.contents().find("body");
    cy.wrap($body)
      .find('input[name="cardnumber"]')
      .type("4242424242424242", { delay: 50 });
  });

  cy.get('iframe[name^="__privateStripeFrame6"]').then(($iframe) => {
    const $body = $iframe.contents().find("body");
    cy.wrap($body).find('input[name="exp-date"]').type("1222", { delay: 10 });
  });
  cy.get('iframe[name^="__privateStripeFrame7"]').then(($iframe) => {
    const $body = $iframe.contents().find("body");
    cy.wrap($body).find('input[name="cvc"]').type("999", { delay: 10 });
  });

  cy.get("button").contains("Submit").click();

  cy.get("#payment-form").should("not.exist");
  cy.get(".message").should(
    "contain",
    "Your order will be ready in 30 minutes!"
  );
});
```

We are adding multiple tests for the different iframes that stripe provides us with, the "credit card numbers" we are passing are given to us by stripe for testing the functionality.

Let us go back to your PaymentForm component and update the return with the rest of the payment fields. Make sure that you import all the stripe components

```js
return (
  <>
    <label>Card number </label>
    <CardNumberElement />
    <label>Expiry Date</label>
    <CardExpiryElement />
    <label>CVC</label>
    <CardCVCElement />
    <button>Submit</button>
  </>
```

Next we want to add a function called `payWithStripe` in the component.

```js
async payWithStripe() {
		await this.props.stripe.createToken().then(response => {
			if (response.token) {
				try {
					this.performPayment(response.token)
				}
				catch {

				}
			}

		})
	}
```

We also want to add a function called performPayment. This function will make the payment.

```js
async performPayment(token) {
  let orderResponse = await axios.put(
    `http://localhost:3000/api/orders/${this.props.orderDetails.id}`,
    {
      activity: 'finalize',
      stripeToken: token
    }
  )
  if (orderResponse.data.paid === true) {
    this.props.finalizeOrder(orderResponse.data.message)

  } else {
    debugger
  }
}
```

And finally, we need to update the button

```js
<button onClick={this.payWithStripe.bind(this)}>Submit</button>
```

Make sure that you have made adequate imports to the component.

Move over to the DisplayProduct component or your main component where you are adding the products to the order.

Update the Props that you are sending through

```js
<PaymentForm
  orderDetails={this.state.orderDetails}
  finalizeOrder={this.finalizeOrder.bind(this)}
/>
```

And update the finalizeOrder function we are passing in the finializeOrder function as a prop to the component.

```js
async finalizeOrder(message) {
  let orderTotal = this.state.orderDetails.order_total
  this.setState({ message: { id: 0, message: message }, orderTotal: orderTotal, orderDetails: {}, showPaymentForm: false })
}
```

Run your tests and they should go green.

Let's move over to the backend
