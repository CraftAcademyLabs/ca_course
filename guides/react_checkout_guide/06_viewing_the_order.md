### Viewing the Order

We are working in very small chunks at the time ensuring that we always are making progress as we move along.

Now it's time for adding the view order functionality to the button.

As always we start with the test.

```js
it("user can add multiple product to order and view its content", () => {
  cy.get("button")
    .contains("View order")
    .should("not.exist");

  cy.get("#product-2").within(() => {
    cy.get("button")
      .contains("Add to order")
      .click();
    cy.get(".message").should(
      "contain",
      "The product has been added to your order"
    );
  });

  cy.get("button")
    .contains("View order")
    .should("exist");

  cy.get("#product-3").within(() => {
    cy.get("button")
      .contains("Add to order")
      .click();
    cy.get(".message").should(
      "contain",
      "The product has been added to your order"
    );
  });

  cy.get("button")
    .contains("View order")
    .click();
  cy.get("#order-details").within(() => {
    cy.get("li").should("have.length", 2);
  });
  cy.get("button")
    .contains("View order")
    .click();
  cy.get("#order-details").should("not.exist");
});
```

Run the test and it is failing....

And now for the implementation code. First we update our state object

```js
state = {
  productData: [],
  message: {},
  orderId: "",
  showOrder: false
};
```

Add the following code to the return block of your component

```js
return (
  <>
    {this.state.orderId !== "" && (
      <button
        onClick={() => {
          this.setState({ showOrder: true });
        }}
      >
        View order
      </button>
    )}
    {this.state.showOrder && (
      <ul id="order-details">
        <li>Item 1</li>
        <li>Item 2</li>
      </ul>
    )}
    {dataIndex}
  </>
);
```

Check if the tests are going green, The last one is probably not. However, we have hardcoded the values here. The best approach should be to pull in this information instead.