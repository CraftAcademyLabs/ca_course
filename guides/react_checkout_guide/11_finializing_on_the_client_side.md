## Finalizing and back to the client

Okay...The final stretch
As always let's start with a new test.

Add the following code to your feature test:

```js
it("user can finalize the order", () => {
  cy.get("#product-2").within(() => {
    cy.get("button").contains("Add to order").click();
  });
  cy.get("#product-3").within(() => {
    cy.get("button").contains("Add to order").click();
  });
  cy.get("button").contains("View order").click();
  cy.route({
    method: "PUT",
    url: "http://localhost:3000/api/orders/1",
    response: { message: "Your order will be ready in 30 minutes!" },
  });
  cy.get("button").contains("Confirm!").click();
  cy.get(".message").should(
    "contain",
    "Your order will be ready in 30 minutes!"
  );
});
```

We also want to make sure that we have som configuration to use in the cypress tests. We are adding a delay depending on the different functions that cypress runs.

```js
// cypress/support/commands.js
const COMMAND_DELAY = 150;

for (const command of [
  "get",
  "visit",
  "click",
  "trigger",
  "type",
  "clear",
  "reload",
  "contains",
]) {
  Cypress.Commands.overwrite(command, (originalFn, ...args) => {
    const origVal = originalFn(...args);

    return new Promise((resolve) => {
      setTimeout(() => {
        resolve(origVal);
      }, COMMAND_DELAY);
    });
  });
}
```

Let's work on the implementation code:

Update the state object with orderTotal

```js
state = {
  productData: [],
  message: {},
  orderDetails: {},
  showOrder: false,
  orderTotal: "",
};
```

Add a new asynchronous function to handle the finalize order

```js
async finalizeOrder() {
  let orderTotal = this.state.orderDetails.order_total
  let result = await axios.put(`http://localhost:3000/api/orders/${this.state.orderDetails.id}`, { activity: 'finalize' })
  this.setState({ message: { id: 0, message: result.data.message }, orderTotal: orderTotal,   orderDetails: {}})
}
```

You also need to update the `addToOrder` function

```js
if (this.state.orderDetails.hasOwnProperty('id') && this.state.orderDetails.finalized === false)
```

And update the return statement with the new changes.

```js
return (
  <>
    {this.state.message.id === 0 && (
      <h2 className="message">{this.state.message.message}</h2>
    )}
    {this.state.orderDetails.hasOwnProperty("products") && (
      <button
        onClick={() => this.setState({ showOrder: !this.state.showOrder })}
      >
        View order
      </button>
    )}
    {this.state.showOrder && (
      <>
        <ul id="order-details">{orderDetailsDisplay}</ul>
        <p>
          To pay: {this.state.orderDetails.order_total || this.state.orderTotal}{" "}
          kr
        </p>
        <button onClick={this.finalizeOrder.bind(this)}>Confirm!</button>
      </>
    )}
    {dataIndex}
  </>
);
```

And finally the render function:

```js
if (this.state.orderDetails.hasOwnProperty("products")) {
  orderDetailsDisplay = this.state.orderDetails.products.map((item) => {
    return <li key={item.name}>{`${item.amount} x ${item.name}`}</li>;
  });
} else {
  orderDetailsDisplay = "Nothing to see";
}
```

Run your tests now and they should be green. We have now finalized the entire creating order functionality both in the front and the backend. Go over your code again and marvel at your creation. This is one approach we can take to create an ordering functionality in React from scratch.

The last thing we need to do is to add our payment gateway.
