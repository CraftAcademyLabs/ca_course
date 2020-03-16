# Adding checkout functionality to your React application

```
This guide is based on a demo that we ran a while back. Please take note that there might be issues for you when adding this functionality as your implementation probably looks different. This is also the first iteration of the guide and updates will be made in the future. 
If there are typos or issues in this guide, please notify a coach 
```

In this section we are going to add checkout functionality to our react application. 
The prerequsitet for this functionality is that we have products on our page that are ready to sell. Please remember as we implement we will omit explaning code that has already been covered. Remember to commit often in case you need to revert back and not lose large sections of the implementation. 

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
      cy.get("button")
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

We need to start with the first step which is to make sure that we have a button available to click on, let's add that where we are maping over the products.

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
	let!(:product_1) {  create(:product, name: 'Pizza')  }
	let!(:product_2) {  create(:product, name: 'Kebab')  }

	it 'responds with success message' do
		post '/api/orders', params: {id: product_1.id }

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


The error message probably has to do with our routes and that there are none present. Let's fix that. 

```ruby
Rails.application.routes.draw do
  namespace :api do
    resources :products, only: [:index]
    resources :orders, only: [:create]
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

Done here and working

------

## Update the order on the Backend

First we need to amend our request spec.

We will amend our specs by adding a before block
```ruby
	before do
		post '/api/orders', params: { id: product_1.id }
		@order = Order.last
  end
```

And adding a new spec 

```ruby
  it 'adds another product to order if param "order_id" is present' do
  post '/api/orders', params {id: product_2.id, order_id: @order.id}
    expect(@order.order_items.count).to eq 2
  end
```


In order to get this to work in the first iteration we can simply refactor our create action in the controller. 

```ruby
class Api::OrdersController < ApplicationController
  def create
    order = if params[:order_id]
              Order.find(params[:order_id])
            else
            Order.create
    order.order_items.create(product_id: params[:product_id])
    render json: { message: 'The product has been added to your order', order_id: order.id }
  end
end
```

Run your test and make sure that this is going green. This is working however this is not the best practice. 

Let's make sure that we follow the conventions and refactor this code so it works. 


Lets refactor our test first
```ruby
  it 'adds another product to order if param "order_id" is present' do
  put "/api/orders/#{order.id}", params: {product_id: product_2.id }
    expect(@order.order_items.count).to eq 2
  end
```

You will probably get an routing error. We will fix that with adding update to the routes:

 `resources :orders, only: [:create, :update]`

Run the test again and you will get a error stating that there is no action update.

```ruby

  def update
    order = Order.find(params[:id])
    product = Product.find(params[:product_id])
    order.order_items.create(product: product)
    render json: { message: 'The product has been added to your order'}
  end
```

And remember to clean out the create action to its previous state:

```ruby
  def create
    order = Order.create
    order.order_items.create(product_id: params[:product_id])
    render json: { message: 'The product has been added to your order' }
  end
```

We will have to refactor our test to better suit our implementation code. The final shape of the test will look like this. 

```rb
RSpec.describe Api::OrdersController, type: :request do
  let!(:product_1) { create(:product, name: 'Pizza') }
  let!(:product_2) { create(:product, name: 'Kebab') }

	before do
		post '/api/orders', params: { product_id: product_1.id }
		@order_id = JSON.parse(response.body)['order_id']
	end

  describe 'POST /api/orders' do
    it 'responds with success message' do
      expect(JSON.parse(response.body)['message']).to eq 'The product has been added to your order'
    end

    it 'responds with order id' do
      order = Order.find(@order_id)
      expect(JSON.parse(response.body)['order_id']).to eq order.id
    end
  end

  describe 'PUT /api/orders/:id' do
    before do
      put "/api/orders/#{@order_id}", params: { product_id: product_2.id }
      @order = Order.find(@order_id)
    end

    it 'adds another product to order if request is a PUT and param id of the order is present' do
      expect(@order.order_items.count).to eq 2
    end

    it 'responds with order id' do
      expect(JSON.parse(response.body)['order_id']).to eq @order.id
    end
  end
end
```

And in the controller:

```rb
class Api::OrdersController < ApplicationController
  def create
    order = Order.create
    order.order_items.create(product_id: params[:product_id])
    render json: { message: 'The product has been added to your order', order_id: order.id }
  end

  def update
    order = Order.find(params[:id])
    product = Product.find(params[:product_id])
    order.order_items.create(product: product)
    render json: { message: 'The product has been added to your order', order_id: order.id }
  end
end
```


### Putting it togheter

In order for the client to work with the backend we need to make some adjustment to the code. 
In your clinent make sure that you add product_id instead of the id.

```js
async addToOrder(event) {
	let id = event.target.parentElement.dataset.id
  let result = await axios.post('http://localhost:3000/api/orders', { product_id: id } )
	this.setState({message: {id: id, message: result.data.message}})
}
```

## Adding multiple products to the order


Let's add the functionality to add multiple products to our order. As always we need to have feature test for that. We will add this to the test that we already have. 

We need to do a couple of things:

Instead of having a `before` function that only runs for one test, we will add a `beforeEach` that will run before all the upcoming feature test. 

We will also add a `PUT` request to this block in order to update the order that has already been created. And at last we will add a test to check that we can add multiple products to an order. 

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

Let's continue with the implementation code to get all of this to work.
add the `orderId` to the state
```js
state = {
		productData: [],
		message: {},
		orderId: ''
	}
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

We also need to make sure that we are adding a order to an existing one, if one already exists so lets add a conditional for that. 

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

So what does this conditional do? We are simply stating that if there is a order and that the `orderId` is not empty, then add the new product to that order with a `PUT` request. Otherwise go ahead and create a new order. 

Run your feature test they should no go green. Go ahead and test this manually aswell. Remember to fire up both the backend and frontend servers. You also need to have some products added to your backend.


Now that we have the main functionality in place we need to start tweaking and making sure that the user experiance is good.

We want to make sure that we can view the products that we have added to our order. 

First we want to make sure that there is a button where we can view the order. 
However this button should only be visable when we have added products to our order. 

Refactor your code to look like this:

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
		{this.state.orderId !== '' && <button>View order</button> }
		{dataIndex}
	</>
)
```	

Run your tests now again and they should go green. Remember that we are only displaying the button, it has no functionality yet. 

We are working in very small chunks at the time ensuring that we always are making progress as we move along. 

Now it's time for adding the view order functionality to the button. 

As always we start with the test.

```js
		cy.get('button').contains('View order').click()
		cy.get('#order-details').within(()=> {
			cy.get('li').should('have.length', 2)})
			
		cy.get('button').contains('View order').click()
		cy.get('#order-details').should('not.exist')
```

And ow for the implementation code. First we update our state object

```js
	state = {
		productData: [],
		message: {},
		orderId: '',
		showOrder: false
	}
```
And then the button
```js
return (
	<>
		{this.state.orderDetails.hasOwnProperty('products') &&
			<button onClick={() => this.setState({ showOrder: true })}>View order</button>
		}
		{this.state.showOrder &&
			<ul id="order-details">
				{orderDetailsDisplay}
			</ul>
		}
		{dataIndex}
	</>
```	

## Viewing the order

Let's work on the view order 

Go to the client, first we willl write the test

```js
	it('user can add multiple product to order and view its content', () => {
		cy.get('button').contains('View order').should('not.exist')

		cy.get('#product-2').within(() => {
			cy.get('button').contains('Add to order').click()
			cy.get('.message').should('contain', "The product has been added to your order")
		})

		cy.get('button').contains('View order').should('exist')

		cy.get('#product-3').within(() => {
			cy.get('button').contains('Add to order').click()
			cy.get('.message').should('contain', "The product has been added to your order")
		})

		cy.get('button').contains('View order').click()
		cy.get('#order-details').within(()=> {
			cy.get('li').should('have.length', 2)
		})
		cy.get('button').contains('View order').click()
		cy.get('#order-details').should('not.exist')

	});
  ```

  Run the test and it is failing....
  Add the following code to the return block of your component

  ```js
    return (
      <>
        {this.state.orderId !== "" 
        && <button onClick={() => {
          this.setState({showOrder: true})
        }
        }>View order</button>}
        {this.state.showOrder && <ul id="order-details">
          <li>Item 1</li>
          <li>Item 2</li>
          </ul>}
        {dataIndex}
      </>
    );
```
Check if the tests are going green, The last one is probably not. However we have hard coded the values here. The best approach should be to pull in this information instead. 

Lets go back to our test and refactor them a little

Add the fixture files to the test

```bash
$ touch cypress/fixtures/put_response.json
$ touch cypress/fixtures/post_response.json
```


zap i should have committed a loong time ago 
(update this with the code from the client)


### Serializers
Lets move go back to our backend to continue this functionality

We want to add a gem called Active Model Serializer
So what is a serializer? A serializer makes sure that our json responses has particular shape and content, rather than the content provided by the built in json responses. We want to be in control of the information that is leaving the application, and in order to do that we are using this gem. 

Add the gem  to your `Gemfile` and run bundle

```rb
gem 'active_model_serializers'
```

Create the file a configuration file where we are adding the settings. 
```bash
$ touch config/initializers/active_model_serializers.rb
```
Add

```rb
ActiveModelSerializers.config.adapter = :json
```

Now lets generate a new serializer. In the future a new serializer will be generated by default if you generate a new model. But for our existing model we will run the generator. 

```bash
$ rails g serializer order
``` 

This generator creates a new file. But before we add code to this (and there is a lot of code to add) we need to refactor our test and implementation to match the serialization.

Go over the following code carefully and take note of the changes we are making. 

`spec/requests/api/client_can_create_new_order_spec.rb`

```rb
RSpec.describe Api::OrdersController, type: :request do
  let!(:product_1) { create(:product, name: "Pizza", price: 10) }
  let!(:product_2) { create(:product, name: "Kebab", price: 20) }

  before do
    post "/api/orders", params: { product_id: product_1.id }
    order_id = JSON.parse(response.body)["order"]["id"]
    @order = Order.find(order_id)
  end

  describe "POST /api/orders" do
    it "responds with success message" do
      expect(JSON.parse(response.body)["message"]).to eq "The product has been added to your order"
    end

    it "responds with order id" do
      expect(JSON.parse(response.body)["order"]["id"]).to eq @order.id
    end

    it "responds with right amount of products" do
      expect(JSON.parse(response.body)["order"]["products"].count).to eq 1
    end

    it "responds with right order total" do
      expect(JSON.parse(response.body)["order"]["total"]).to eq 10
    end
  end

  describe "PUT /api/orders/:id" do
    before do
      put "/api/orders/#{@order.id}", params: { product_id: product_2.id }
      put "/api/orders/#{@order.id}", params: { product_id: product_2.id }
    end

    it "adds another product to order if request is a PUT and param id of the order is present" do
      expect(@order.order_items.count).to eq 3
    end

    it "responds with order id" do
      expect(JSON.parse(response.body)["order"]["id"]).to eq @order.id
    end
    
    it "responds with right amount of unique products" do
      expect(JSON.parse(response.body)["order"]["products"].count).to eq 2
    end

    it "responds with right order total" do
      expect(JSON.parse(response.body)["order"]["total"]).to eq 50
    end
  end
end
```

And now for the serializer

`app/serializers/order_serializer.rb`
```rb
class OrderSerializer < ActiveModel::Serializer
  attributes :id, :products_1, :products_2, :products_3, :products, :total, :order_total

  def products_1
    products = []
    object.order_items.group_by(&:product_id).each do |key, value|
      product = Product.find(key)
      hash = { amount: value.count, name: product.name, total: (product.price * value.count) }
      products.push hash
    end
    products
  end

  def products_2
    products = []
    unique_items = object.order_items.uniq(&:product)
    unique_items_count = object.order_items.group_by(&:product_id).map { |key, value| [key, value.size] }.to_h
    unique_items.each do |item|
      products.push(
        amount: unique_items_count[item.product_id],
        name: item.product.name,
        total: (unique_items_count[item.product_id] * item.product.price)
      )
    end
    products
  end

  def products_3
    object.order_items.group_by(&:product_id).map do |_key, value|
      product = value.uniq(&:product_id)[0].product
      { amount: value.size, name: product.name, total: (value.size * product.price) }
    end
	end
	alias_method :products, :products_3

  def total
    object.order_items.joins(:product).sum('products.price')
  end
end
```

And finally we need to update our controller with the changes.

`app/controllers/api/orders_controller.rb`

```rb
class Api::OrdersController < ApplicationController
  def create
    order = Order.create
    order.order_items.create(product_id: params[:product_id])
    render json: create_json_response(order)
  end

  def update
    order = Order.find(params[:id])
    product = Product.find(params[:product_id])
    order.order_items.create(product: product)
    render json: create_json_response(order)
  end

private

  def create_json_response(order)
    json = { order: OrderSerializer.new(order) }
    json.merge!(message: 'The product has been added to your order')
  end
end
```

## Go back to the frontend


Now we want to go back to our frontend and add the changes there as well. We need to  make sure that the order totals are showing up. 

As always we first start with our tests. 

```js
    cy.get("button")
      .contains("View order")
      .click();
    cy.get("#order-details").within(() => {
      cy.get("li")
        .should("have.length", 2)
        .first()
        .should("have.text", "1 x Salad")
        .next()
        .should("have.text", "1 x Ice Cream");
    });
```

We also need to import the DisplayProductData to the feature file. 

`import DisplayProductData from '../../src/components/DisplayProductData'`

And of course we need to update our fixture files to accomodate the new changes

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
And Finally our implementation code. 

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

And in our render function
```js
return <li key={item.name}>{`${item.amount} x ${item.name}`}</li>
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

commit this and lets go back to the backend for finilazing the order. 

first we need to create a new request spec

`$ touch spec/requests/api/user_can_finalize_order_spec.rb`

inside of this file add the followign specs

```rb
RSpec.describe Api::OrdersController, type: :request do
  let(:product_1) { create(:product, name: 'Pizza', price: 10) }
  let(:product_2) { create(:product, name: 'Kebab', price: 20) }
  let(:order) { create(:order) }

  before do
    order.order_items.create(product: product_1)
    order.order_items.create(product: product_2)

    put "/api/orders/#{order.id}", params: { activity: 'finalize' }
  end

  describe 'PUT /api/orders' do
    it 'responds with success message' do
      expect(JSON.parse(response.body)['message']).to eq 'Your order will be ready in 30 minutes!'
		end

		it 'sets the order attribute "finalized" to true' do
			expect(order.reload.finalized).to eq true
		end
  end
end
```

Run the specs you should and take notice of the error messages. 

We need to add a new column in our orders database table. 

`rails g migration AddFinalizedToOrders finalized:boolean`

Before you migrate make sure that it is adding a column to the orders db table and make sure that the default value is set to false.

It should look like this:


```rb
class AddFinalizedToOrders < ActiveRecord::Migration[6.0]
  def change
    add_column :orders, :finalized, :boolean, default: false
  end
end
```
run the migrations and make sure that the changes have been added to your schema file.

The final step we need to take is to refactor our update action. 

`app/controllers/api/orders_controller.rb`

```rb
	def update
		order = Order.find(params[:id])
		if params[:activity]
			order.update_attribute(:finalized, true)
      render json: { message: 'Your order will be ready in 30 minutes!' }
    else
      product = Product.find(params[:product_id])
      order.order_items.create(product: product)
      render json: create_json_response(order)
    end
  end
```

Remember that need to update our order serializer
```rb
attributes :id, :products_1, :products_2, :products_3, :products, :total, :order_total, :finalized
```


Run your spec they should be green by now. 

Lets head back over to the client.

## Finalizing and back to the client

As always let's start with a new spec. 

Add the following code to your feature test:

```js
it('user can finalize the order', () => {
		cy.get('#product-2').within(() => {
			cy.get('button').contains('Add to order').click()
		})
		cy.get('#product-3').within(() => {
			cy.get('button').contains('Add to order').click()
		})
		cy.get('button').contains('View order').click()
		cy.route({
			method: 'PUT',
			url: 'http://localhost:3000/api/orders/1',
			response: { message: 'Your order will be ready in 30 minutes!' }
		})
		cy.get('button').contains('Confirm!').click()
		cy.get('.message').should('contain', "Your order will be ready in 30 minutes!")
}); 

```

We also want to make sure that we have som econfigration to use in the cypress tests. We are adding a delay depending on the different functions that cypress runs. 

```js

// cypress/support/commands.js
const COMMAND_DELAY = 150;


for (const command of ['get', 'visit', 'click', 'trigger', 'type', 'clear', 'reload', 'contains']) {
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

Now for the implementation code:

Update the state object with orderTotal
```js
state = {
		productData: [],
		message: {},
		orderDetails: {},
		showOrder: false,
		orderTotal: ''
	}
```

Add a new asyncronous function to handle the finalize order 

```js
	async finalizeOrder() {
		let orderTotal = this.state.orderDetails.order_total
		let result = await axios.put(`http://localhost:3000/api/orders/${this.state.orderDetails.id}`, { activity: 'finalize' })
		this.setState({ message: { id: 0, message: result.data.message }, orderTotal: orderTotal,   orderDetails: {}})
	}
```
You also need to update the addToOrder function

```js
if (this.state.orderDetails.hasOwnProperty('id') && this.state.orderDetails.finalized === false)
```

And update the return statement with the new changes. 

```js
return (
			<>
				{this.state.message.id === 0 &&
					<h2 className='message'>{this.state.message.message}</h2>
				}
				{this.state.orderDetails.hasOwnProperty('products') &&
					<button onClick={() => this.setState({ showOrder: !this.state.showOrder })}>View order</button>
				}
				{this.state.showOrder &&
					<>
						<ul id="order-details">
							{orderDetailsDisplay}
						</ul>
						<p>To pay: {this.state.orderDetails.order_total || this.state.orderTotal} kr</p>
						<button onClick={this.finalizeOrder.bind(this)}>Confirm!</button>
					</>
				}
				{dataIndex}
			</>
		)
```

And finally the render function:

```js
{`${item.name} ${item.description} - ${item.price}kr `}
```

Run your tests now and they should be green. 

We have now finalized the entire creating order functionality both in the front and the backend. 

