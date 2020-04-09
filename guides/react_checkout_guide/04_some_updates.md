## Update the order on the Backend

We have added a lot of functionality and e need to revisit our specs and make some updates to have it work on the client-side later on.

First, we need to amend our request spec `client_can_create_new_order_spec`.

We will amend our specs by adding a `before` block

```ruby
before do
  post '/api/orders', params: { id: product_1.id }
  @order = Order.last
end
```

And adding a new spec

```ruby
it 'adds product to order if param "order_id" is present' do
  post '/api/orders', params: {id: product_2.id, order_id: @order.id}
  expect(@order.order_items.count).to eq 2
end
```

And 

```ruby
it 'adds another product to order if param "order_id" is present' do
  put "/api/orders/#{order.id}", params: {product_id: product_2.id }
  expect(@order.order_items.count).to eq 2
end
```




You will probably get a routing error. We will fix that with adding update & create to the routes:

`resources :orders, only: [:create, :update]`

Run the test again and you will get a error stating that there is no action `update`.

```ruby

  def update
    order = Order.find(params[:id])
    product = Product.find(params[:product_id])
    order.order_items.create(product: product)
    render json: { message: 'The product has been added to your order'}
  end
```

And the create action:

```ruby
  def create
    order = Order.create
    order.order_items.create(product_id: params[:product_id])
    render json: { message: 'The product has been added to your order' }
  end
```

We will have to refactor our test to better suit our implementation code, dy dividing both functionalities in out tests. The final shape of the test will look like this.

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