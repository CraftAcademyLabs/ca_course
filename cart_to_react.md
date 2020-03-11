## Adding cart functionality to React

In this section we are going to add checkout functionality to our react application. 
The prerequsitet for this functionality is that we have products on our page that are ready to sell. 

Start with making sure that you have the latest code pulled from github on your development branch and create a new branch called `add_order_functionality` or something that you think is appropriate to the user story.

As always we will work in a test driven way. Making sure that we let our acceptance test guide our development. This will ensure us that we are not biting off more than we can chew. But also that we stay in scope.


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
      response: "fixture:product_data.json"
    });
    
    cy.route({
      method: "POST",
      url: "http://localhost:3000/api/orders",
      response: { message: "A product has been added to your order" }
    });
  });
  
  it("user gests a confirmation messsage when adding a a producut to order", () => {
    cy.visit("http://localhost:3001"); // Make sure to point it to port 3001 as we are using 3000 for our API
    cy.get("#product-1").within(() => {
      cy.get("#button")
        .contains("Add to order")
        .click();
    });
    cy.wait(3000)
  });
});
```
So what are we excpecting to see in the implementation. Well we need to make sure that we can add a product to an order. This means that we need to have a list of products since before. After that we need to make sure that there is a button to make this happen. And it will all end with us getting a confirmation message that we have added a product to our order. Simple huh?

When you run your test now it should all fail and in order to make it work we have to add the actual implementaion code. 

In our case we already have a component that we use in order to display the products, all we have to do now is to add the button to the iteration and then make sure that we can see the message. 

Let's add the implementation code. 

We need to start with the first step which is to make sure that we have a button available to click on, let's add that.

```jsx
<div key={item.id} id={`product-${item.id}`}>
  {`${item.name} ${item.description} ${item.price}`}
  <button onClick={this.addToOrder.bind(this)}>Add to order</button>
</div>
```
So here we have added a `addToOrder` function that is not defined. Lets go ahead and add that functionality as well. 

Add the following code to the component:

```js
addToOrder() {
  debugger
}
```

So why are we adding a debugger here? Well first we need to make sure that the `addToOrder` button actually gets invoked. 

Run your tests now. The debugger should kick in and halt the execution, if not then make sure that you have added React developer tools to your chrome broweser. 

If we can see that then its time for us to add the real functionality. 
But first lets make sure that we are hitting the right button. 
Go to your chrome console where the execution of the code has stopped and run the following command in the console:

`event.target.parentElement`

You should probably see something like this in your console:

```html
<div id="product-1">
  "Pizza"
  "Crusty and fluffy"
  "100 SEK"
  <button>Add to order</button>
</div>
```
If not then make sure that you are iterating over your products properly. 

So this was our first iteration. But we need to make sure that we are pulling the information of the product as a dataset rather than through an id, and the reason for that is that it will be easier for us to manage. But it will also help us use it beacuse the values in the product are sent back to us in the form of an object.

Add the dataset to the parent div:

```js
	<div key={item.id} id={`product-${item.id}`} data-id={item.id} data-price={item.price}>
		{`${item.name} ${item.description} ${item.price}`}
		<button onClick={this.addToOrder.bind(this)}>Add to order</button>
  </div>
```

Now if you run your test, the debugger should kick in, run the following command in the console `event.target.parentElement.dataset`.
you should see the same information as before, but the information about the product will be returend to us in the form of an object. 

This is good new for us beacuse we can use this in our `addToOrder` function. Time to add the rest of the implementation code to the `addToOrder` function. 

```js
async addToOrder(event) {
	let id = event.target.parentElement.dataset.id
  let result = await axios.post('http://localhost:3000/api/orders', { id: id } )
}
```

So what did we do here? First we made the method asyncronous beacuse we need to wait for a response from our API, we passed in the event as an argument to the function and we also added axios in order to handle that call to the API. the last thing that we added was a post request that we save in the variable `result`

Make sure that you add axios and also import it to the top of the file if you haven't already.

The next thing we are going to focus on is to get the message to be displayed.

Lets start with adding the message to the state:

```js
state = {
		productData: [],
		message: {}
	}
```

Next we need to display the message below the button. 

```js
[....]
<button onClick={this.addToOrder.bind(this)}>Add to order</button>
	{parseInt(this.state.message.id) === item.id && <p class='message'>{this.state.message.message}</p>}
```

Finally we need to set the new state in the `addToOrder` function

```js
async addToOrder(event) {
	let id = event.target.parentElement.dataset.id
  let result = await axios.post('http://localhost:3000/api/orders', { id: id } )
	this.setState({message: {id: id, message: result.data.message}})
}
```

If we run our tests now everything should go green. 

Lets move over to the backend and start with creating a spec that we are going to use. Make sure that you create a new branch to work on. 

```bash
$ touch spec/requests/api/client_can_create_new_order_spec.rb
```

Add the following test to the spec file

```ruby
RSpec.describe Api::OrdersController, type: :request do
  let!(:product_1) { create(:product, name: 'Pizza') }
  let!(:product_2) { create(:product, name: 'Kebab') }

	before do
		post '/api/orders', params: { product_id: product_1.id }
		@order_id = JSON.parse(response.body)['order_id']
	end

  it 'responds with success message' do
    expect(JSON.parse(response.body)['message']).to eq 'The product has been added to your order'
  end
end
```
Remember that the request spec acts like our feature test but for APIs. This will allow us to see what what requests we are sending out. 

Run the test and make sure that you are getting the correct error messages. 

We will probably get an error message that states that we have an unitialized constant Api::OrdersController.

We will fix this by generating a new orders controller. 

`$ rails generate controller Api::Orders create`

Run the test again and you will get a new error message. 


The error message probably has to do with our routes and that there are none. Lets fix that. 

```ruby
Rails.application.routes.draw do
  namespace :api do
    resources :products, only: [:index]
    resources :orders, only: [:create, :update]
  end
end 
```

Our problem now is that we are not getting the right response. But this is expected. 

To fix this we need to add the order to the create action in our Orders controller.

```ruby
class Api::OrdersController < ApplicationController
  def create
    order = Order.create
    order.order_items.create(product_id: params[:product_id])
    render json: { message: 'The product has been added to your order', order_id: order.id }
  end
end
```

The next step we need to take is to generate a Order model to have something to pull out from the database. 

`$ rails generate model order` 

We also need to associate the the order to the order items that we have, and the products.

`$ rails generate model OrderItems order:references product:references`

Go ahead and go through the files that are generated and make sure that there are no problems.

We will now add specs to test the associations we created between the models. 
In the newly generated `order_items_spec.rb` go ahead and add two specs. 
You will need more than that but for this guide we will limit ourselves to these specs. 

```ruby
require 'rails_helper'

RSpec.describe OrderItem, type: :model do
  it { is_expected.to belong_to :order }
  it { is_expected.to belong_to :product }
end
```

`order_spec.rb`

```ruby
require 'rails_helper'

RSpec.describe Order, type: :model do
	it {is_expected.to have_many :order_items}
end
```

We need to make sure that the associations are present as well.

`order_item.rb`

```ruby
class OrderItem < ApplicationRecord
  belongs_to :order
  belongs_to :product
end
```

`order.rb`

```ruby

class Order < ApplicationRecord
	has_many :order_items
end
```

Run your migrations and then run your specs. Everything should be green like Bruce Banners alter ego. 


### Returning to the client

Let's go back tot the client and add the functionality to add multiple products to our order. As always we need to have feature test for that. We will add this to the test that we already have. 

We need to do a couple of things:

Instead of having a `before` function that only runs for one test, we will add a `beforeEach` that will run before all the upcoming feature test. 

We will also add a `PUT` request to this block in order to update the order that has already been created. 

And at last we will add a test to check that we can add multiple products to an order. 

The final form of the feature will be the following:

```js
describe('User can add a product to his/her order', () => {

	beforeEach(() => {
		cy.server();
		cy.route({
		method: 'GET',
		url: 'http://localhost:3000/api/products',
		response: 'fixture:product_data.json'
	})

	cy.route({
		method: 'POST',
		url: 'http://localhost:3000/api/orders',
		response: { message: 'The product has been added to your order', order_id: 1 }
	})

	cy.route({
		method: 'PUT',
		url: 'http://localhost:3000/api/orders/1',
		response: { message: 'The product has been added to your order', order_id: 1 }
	})
	cy.visit('http://localhost:3001')
});

	it('user get a confirmation message when adding product to order', () => {
		cy.get('#product-2').within(() => {
		cy.get('button').contains('Add to order').click()
		cy.get('.message').should('contain', "The product has been added to your order")
		})

		cy.get('#product-3').within(() => {
		cy.get('button').contains('Add to order').click()
		cy.get('.message').should('contain', "The product has been added to your order")
		})
	});
});
```

Make sure that you have added the product_id to the messages in the `beforeEach` function. 
