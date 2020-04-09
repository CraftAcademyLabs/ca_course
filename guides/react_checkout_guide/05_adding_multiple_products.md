## Adding multiple products to the order

Let's add the functionality to add multiple products to our order. As always we need to have a feature test for that. We will add this to the test that we already have.
We need to do a couple of things:

Instead of having a `before` function that only runs for one test, we will add a `beforeEach` that will run before all the upcoming feature test.

We will also add a `PUT` request to this block in order to update the order that has already been created. And at last, we will add a test to check that we can add multiple products to an order.

The final form of the feature will be the following:

```js
describe("User can add a product to his/her order", () => {
  beforeEach(() => {
    cy.server();
    cy.route({
      method: "GET",
      url: "http://localhost:3000/api/products",
      response: "fixture:product_data.json",
    });

    cy.route({
      method: "POST",
      url: "http://localhost:3000/api/orders",
      response: {
        message: "The product has been added to your order",
        order_id: 1,
      },
    });

    cy.route({
      method: "PUT",
      url: "http://localhost:3000/api/orders/1",
      response: {
        message: "The product has been added to your order",
        order_id: 1,
      },
    });
    cy.visit("http://localhost:3001");
  });

  it("user get a confirmation message when adding product to order", () => {
    cy.get("#product-2").within(() => {
      cy.get("button").contains("Add to order").click();
      cy.get(".message").should(
        "contain",
        "The product has been added to your order"
      );
    });

    cy.get("#product-3").within(() => {
      cy.get("button").contains("Add to order").click();
      cy.get(".message").should(
        "contain",
        "The product has been added to your order"
      );
    });
  });
});
```

Make sure that you have added the product_id to the messages in the `beforeEach` function.

Let's continue with the implementation code to get all of this to work.

Add the `orderId` to the state:

```js
state = {
  productData: [],
  message: {},
  orderId: "",
};
```

Now we need to update the `addToOrder` function with the new `orderId` state.

```js
async addToOrder(event) {
	let id = event.target.parentElement.dataset.id
  let result = await axios.post('http://localhost:3000/api/orders', { id: id } )
	this.setState({message: {id: id, message: result.data.message}, orderId: results.data.order_id})
}
```

The `order_id` is what is being returned to us from the backend.

We also need to make sure that we are adding an order to an existing one, if one already exists so let's add a conditional for that.

```js
async addToOrder(event) {
  let id = event.target.parentElement.dataset.id
  let result
  if (this.state.orderId !== "") {
    result = await axios.put(`http://localhost:3000/api/orders/${this.state.orderId}`, { product_id: id })
  } else {
    result = await axios.post('http://localhost:3000/api/orders', { product_id: id } )
  }
  this.setState({message: {id: id, message: result.data.message}, orderId: result.data.order_id})
}
```

So what does this conditional do? We are simply stating that if there is an `order` and that the `orderId` is not empty, then add the new product to that order with a `PUT` request. Otherwise, go ahead and create a new order.

Run your feature test they should no go green. Go ahead and test this manually as well. Remember to fire up both the backend and frontend servers. You also need to have some products added to your backend.

Now that we have the main functionality in place we need to start tweaking and making sure that the user experience is good.

We want to make sure that we can view the products that we have added to our order.
First, we want to make sure that there is a button where we can view the order.
However, this button should only be visible when we have added products to our order.

As a best practice, we will go back to our tests and refactor them a little. Refactor your code to look like this:

```js
	[....]

	it('user can add multiple products to order and view its content', () => {
		cy.get('button').contains('View order').should('not.exist') // Add this line
		cy.get('#product-2').within(() => {
		cy.get('button').contains('Add to order').click()
		cy.get('.message').should('contain', "The product has been added to your order")
		})

		cy.get('button').contains('View order').should('exist') // Add this line
		cy.get('#product-3').within(() => {
		cy.get('button').contains('Add to order').click()
		cy.get('.message').should('contain', "The product has been added to your order")
		})
	});
});
```

And now for the implementation, let's add the button in the `DisplayProducts` component

```js
return (
  <>
    {this.state.orderId !== "" && <button>View order</button>}
    {dataIndex}
  </>
);
```

Run your tests now again and they should go green. Remember that we are only displaying the button, it has no functionality yet.
