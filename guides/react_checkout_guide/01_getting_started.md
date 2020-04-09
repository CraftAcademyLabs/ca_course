## Getting started

Create a new feature file by typing  
`$ touch cypress/integration/userCanAddProductsToOrder.feature.js`

Add the following tests to the file:

```js
describe("User can add a product to their order", () => {
  before(() => {
    cy.server();
    cy.route({
      method: "GET",
      url: "http://localhost:3000/api/products",
      response: "fixture:product_data.json",
    });

    cy.route({
      method: "POST",
      url: "http://localhost:3000/api/orders",
      response: { message: "A product has been added to your order" },
    });
  });

  it("user gests a confirmation messsage when adding a a producut to order", () => {
    cy.visit("/");
    cy.get("#product-1").within(() => {
      cy.get("button").contains("Add to order").click();
    });
    cy.contains("A product has been added to your order");
  });
});
```

So what are we expecting to see in the implementation? Well, we need to make sure that we can add a product to an order. This means that we need to have a list of products since before. After that, we need to make sure that there is a button to make this happen. And it will all end with us getting a confirmation message that we have added a product to our order. Simple huh?

When you run your test now it should all fail and to make it work we have to add the actual implementation code.

In our case we already have a component that we use to display the products, all we have to do now is to add the button to the iteration and then make sure that we can see the message.

Let's add the implementation code.

We need to start with the first step which is to make sure that we have a button available to click on, let's add that where we are mapping over the products, for example `DisplayProductData`

```jsx
dataIndex = (
  <div id="index">
    {this.state.productData.map((item) => {
      return (
        <div key={item.id} id={`product-${item.id}`}>
          {`${item.name} ${item.description} ${item.price}`}
          <button onClick={this.addToOrder.bind(this)}>Add to order</button>
        </div>
      );
    })}
  </div>
);
```

So here we have added a `addToOrder` function that is not defined. Lets go ahead and add that functionality as well.

Add the following code to the component:

```js
addToOrder() {
  debugger
}
```

So why are we adding a debugger here? Well, first we need to make sure that the `addToOrder` button gets invoked.

Run your tests now. The debugger should kick in and halt the execution, if not then make sure that you have added React developer tools to your chrome browser.

If we can see that then its time for us to add the real functionality.

But first let's make sure that we are hitting the right button.

Go to your chrome console where the execution of the code has stopped and run the following command in the console:

`event.target.parentElement`

You should probably see something like this in your console:

```html
<div id="product-1">
  "Pizza" "Crusty and fluffy" "100 SEK"
  <button>Add to order</button>
</div>
```

If not then make sure that you are iterating over your products properly.
So this was our first iteration. But we need to make sure that we are pulling the information of the product as a dataset rather than through an id, and the reason for that is that it will be easier for us to manage. But it will also help us use it because the values in the product are sent back to us in the form of an object.

Add the dataset to the parent div:
`data-id={item.id} data-price={item.price}`

Like this:

```js
return (
  <div
    key={item.id}
    id={`product-${item.id}`}
    data-id={item.id}
    data-price={item.price}
  >
    {`${item.name} ${item.description} ${item.price}`}
    <button onClick={this.addToOrder.bind(this)}>Add to order</button>
  </div>
);
```

Now if you run your test, the debugger should kick in, run the following command in the console `event.target.parentElement.dataset`.
You should see the same information as before, but the information about the product will be returned to us in the form of an object. This is good news for us because we can use this in our `addToOrder` function. Time to add the rest of the implementation code to the `addToOrder` function.

```js
async addToOrder(event) {
	let id = event.target.parentElement.dataset.id
  let result = await axios.post('http://localhost:3000/api/orders', { id: id } )
}
```

So what did we do here? First, we made the method asynchronous because we need to wait for a response from our API, we passed in the event as an argument to the function and we also added `axios` to handle that call to the API. the last thing that we added was a post request that we save in the variable `result`

Make sure that you add `axios` and also import it to the top of the file if you haven't already.
