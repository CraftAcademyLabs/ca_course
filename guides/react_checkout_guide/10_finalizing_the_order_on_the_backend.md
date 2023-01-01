## Finalizing the order

We will start by adding the functionality to the backend. 
First, we need to create a new request spec

`$ touch spec/requests/api/user_can_finalize_order_spec.rb`

Inside of this file add the following specs

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

Before you migrate make sure that it is adding a column to the orders db:table and make sure that the default value is set to false.
It should look like this:

```rb
class AddFinalizedToOrders < ActiveRecord::Migration[6.0]
  def change
    add_column :orders, :finalized, :boolean, default: false
  end
end
```

Run the migrations and make sure that the changes have been added to your schema file. The final step we need to take is to refactor our update action.

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

Remember that need to update our order serializer as well.

```rb
attributes :id, :products_1, :products_2, :products_3, :products, :total, :order_total, :finalized
```

Run your spec they should be green by now.

Let's head back over to the client.