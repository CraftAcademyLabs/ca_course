## Go back to the frontend

Now we want to go back to our frontend and add the changes there as well. We need to make sure that the order totals are showing up.

As always we first start with our tests. Refactor your test by adding the following section to your it block in your test file.

```js
[....]
cy.get('button').contains('View order').click()

cy.get('#order-details').within(() => {
  cy.get('li')
    .should('have.length', 2)
    .first().should('have.text', '1 x Salad')
    .next().should('have.text', '1 x Ice Cream')
})

cy.get('button').contains('View order').click()
cy.get('#order-details').should('not.exist')
```

We also need to import the `DisplayProductData` to the feature file.

`import DisplayProductData from '../../src/components/DisplayProductData'`

And of course, we need to update our fixture files to accommodate the new changes

```json
// put_response.json

{
  "message": "The product has been added to your order",
  "order": {
    "id": 1,
    "products": [
      {
        "amount": 1,
        "name": "Salad",
        "price": 4.0
      },
      {
        "amount": 1,
        "name": "Ice Cream",
        "price": 3.75
      }
    ],
    "order_total": 7.75,
    "finalized": false
  }
}
```

and

```json
// post_response.json
{
  "message": "The product has been added to your order",
  "order": {
    "id": 1,
    "products": [
      {
        "amount": 1,
        "name": "Salad",
        "price": 4.0
      }
    ],
    "order_total": 7.75,
    "finalized": false
  }
}
```

And finally our implementation code.

First in our `addToOrder` function
`this.setState({ message: { id: id, message: result.data.message }, orderDetails: result.data.order })`

So the function looks like this

```js
	async addToOrder(event) {
		let id = event.target.parentElement.dataset.id
		let result
		if (this.state.orderDetails.hasOwnProperty('id')) {
			result = await axios.put(`http://localhost:3000/api/orders/${this.state.orderDetails.id}`, { product_id: id })
		} else {
			result = await axios.post('http://localhost:3000/api/orders', { product_id: id })
		}
		this.setState({ message: { id: id, message: result.data.message }, orderDetails: result.data.order })
```

And in our if statement in the orderDetailDisplay

```js
orderDetailsDisplay = this.state.orderDetails.products.map((item) => {
  return <li key={item.name}>{`${item.amount} x ${item.name}`}</li>;
});
```

And finally in the return

```js
return (
  <>
    {this.state.orderDetails.hasOwnProperty('products') &&
      <button onClick={() => this.setState({ showOrder: !this.state.showOrder })}>View order</button>
    }
    {this.state.showOrder &&
      <>
        <ul id="order-details">
          {orderDetailsDisplay}
        </ul>
        <p>To pay: {this.state.orderDetails.order_total}</p>
      </>
    }
    {dataIndex}
  </>
```

Commit this and let's go back to the backend to finalize the order.
