## Displaying messages and the Backend

The next thing we are going to focus on is to get the message to be displayed.
Let's start with adding the message to the state:

```js
state = {
  productData: [],
  message: {},
};
```

Next, we need to display the message below the button.

```js
[....]
<button onClick={this.addToOrder.bind(this)}>Add to order</button>
{parseInt(this.state.message.id) === item.id && 
<p class='message'>{this.state.message.message}</p>}
```

Finally, we need to set the new state in the `addToOrder` function

```js
async addToOrder(event) {
	let id = event.target.parentElement.dataset.id
  let result = await axios.post('http://localhost:3000/api/orders', { id: id } )
	this.setState({message: {id: id, message: result.data.message}})
}
```

If we run our tests now everything should go green.

Let's move over to the backend and  work on our API. We will start with creating a spec. Make sure that you create a new branch to work on.

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

Remember that the request spec acts like our feature test but for APIs. This will allow us to see what requests we are sending out.

Run the test and make sure that you are getting the correct error messages.

We will probably get an error message that states that we have an uninitialized constant `Api::OrdersController`.

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
We are getting a new error: 
```bash
NameError: uninitialized constant Api::OrdersController::Order 
```
To fix this we need to create a new model
Cool. let's move over to the model.
