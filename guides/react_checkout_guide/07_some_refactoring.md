Some quick refactoring

Let's go back to our test and refactor them a little.
We want to use fixture files in order to mock the responses that we should be getting, instead of having the information our feature files.

Start by creating to fixtures, one for the put request and one for the post request.

```bash
$ touch cypress/fixtures/put_response.json
$ touch cypress/fixtures/post_response.json
```

Add the fixture files to the test

`cypress/fixtures/post_response.json`

```json
{
  "message": "The product has been added to your order",
  "order_details": {
    "order": {
      "id": 1,
      "products": [
        {
          "name": "Salad",
          "price": 4.0
        }
      ]
    }
  }
}
```

And the other in the `cypress/fixtures/put_response.json`

```json
{
  "message": "The product has been added to your order",
  "order_details": {
    "order": {
      "id": 1,
      "products": [
        {
          "name": "Salad",
          "price": 4.0
        },
        {
          "name": "Ice Cream",
          "price": 3.75
        }
      ]
    }
  }
}
```

You also need to refactor your tests to look like this:

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
      response: "fixture:post_response.json", // use the fixture here
    });

    cy.route({
      method: "PUT",
      url: "http://localhost:3000/api/orders/1",
      response: "fixture:put_response.json",  // use the fixture here
    });
    cy.visit("http://localhost:3001");
  });

  it("user can add multiple product to order and view its content", () => {
    cy.get("button").contains("View order").should("not.exist");

    cy.get("#product-2").within(() => {
      cy.get("button").contains("Add to order").click();
      cy.get(".message").should(
        "contain",
        "The product has been added to your order"
      );
    });

    cy.get("button").contains("View order").should("exist");

    cy.get("#product-3").within(() => {
      cy.get("button").contains("Add to order").click();
      cy.get(".message").should(
        "contain",
        "The product has been added to your order"
      );
    });

    cy.get("button").contains("View order").click();

    cy.get("#order-details").within(() => {
      cy.get("li").should("have.length", 2);
    });

    cy.get("button").contains("View order").click();

    cy.get("#order-details").should("not.exist");
  });
});
```

And finally our implementation code:
It should look like this after you have refactored it.

```js
import React, { Component } from "react";
import { getData } from "../modules/productData";
import axios from "axios";

class DisplayProductData extends Component {
  state = {
    productData: [],
    message: {},
    orderDetails: {},
    showOrder: false,
  };

  componentDidMount() {
    this.getProductData();
  }

  async getProductData() {
    let result = await getData();
    this.setState({ productData: result.data.products });
  }

  async addToOrder(event) {
    let id = event.target.parentElement.dataset.id;
    let result;
    if (this.state.orderDetails.hasOwnProperty("id")) {
      result = await axios.put(
        `http://localhost:3000/api/orders/${this.state.orderDetails.id}`,
        { product_id: id }
      );
    } else {
      result = await axios.post("http://localhost:3000/api/orders", {
        product_id: id,
      });
    }
    this.setState({
      message: { id: id, message: result.data.message },
      orderDetails: result.data.order_details.order,
    });
  }

  render() {
    let dataIndex, orderDetailsDisplay;
    if (
      Array.isArray(this.state.productData) &&
      this.state.productData.length
    ) {
      dataIndex = (
        <div id="index">
          {this.state.productData.map((item) => {
            return (
              <div
                key={item.id}
                id={`product-${item.id}`}
                data-id={item.id}
                data-price={item.price}
              >
                {`${item.name} ${item.description} ${item.price}`}
                <button onClick={this.addToOrder.bind(this)}>
                  Add to order
                </button>
                {parseInt(this.state.message.id) === item.id && (
                  <p className="message">{this.state.message.message}</p>
                )}
              </div>
            );
          })}
        </div>
      );
    }

    if (this.state.orderDetails.hasOwnProperty("products")) {
      orderDetailsDisplay = this.state.orderDetails.products.map((item) => {
        return <li key={item.name}>{item.name}</li>;
      });
    } else {
      orderDetailsDisplay = "Nothing to see";
    }

    return (
      <>
        {this.state.orderDetails.hasOwnProperty("products") && (
          <button
            onClick={() => this.setState({ showOrder: !this.state.showOrder })}
          >
            View order
          </button>
        )}
        {this.state.showOrder && (
          <ul id="order-details">{orderDetailsDisplay}</ul>
        )}
        {dataIndex}
      </>
    );
  }
}
export default DisplayProductData;
```
Your code might look different at this stage, if you are using different naming for the components. But this is how ours look. Take a moment to go over your code. Are there any further rooms for improvements? ask yourself if we can divide the code into more components or perhaps modules? Make sure that you note potential improvments that you and your team could make here.

Run the test and move to the next section.
