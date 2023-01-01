## Stripe in the Backend

Time to work on the backend. Make sure that you create a new branch here as well before you get started.

Start with creating a request spec.

```bash
$ touch spec/requests/api/client_can_send_payment_request_spec.rb
```

Add the following test

```rb
RSpec.describe Api::OrdersController, type: :request do
  let(:product_1) { create(:product, price: 50) }
  let(:order) { create(:order) }
  let!(:order_item) { create(:order_item, product: product_1, order: order) }

  before do
    put "/api/orders/#{order.id}",
        params: { activity: "finalize",
                  stripetoken: "12345",
                  email: 'testing@test.com'
                }
  end

  it 'user can pay for the order' do
    expect(JSON.parse(response.body)).to eq JSON.parse('{"paid": true, "message": "Your order will be ready in 30 minutes!"}')
  end
end
```

Run your tests and the should be failing. That is a good thing because now we know what to do next.

Head over to your orders controller and add the following functionality to the update action

```rb
def update
  order = Order.find(params[:id])
  if params[:activity]
    payment_status = perform_stripe_payment

    if payment_status == true
      # successful payment
      order.update_attribute(:finalized, true)
      render json: { paid: true, message: 'Your order will be ready in 30 minutes!' }
    else
      # unsuccessful payment
    end

  else
    product = Product.find(params[:product_id])
    order.order_items.create(product: product)
    render json: create_json_response(order)
  end
end
```

We are not dealing with the unsuccessful payment. Make sure that you add and test for an error message once we are done with the Happy path.

You will get a Name error stating that there is no `perform_stripe_payment`

Let's fix that. In the private section of the `orders` controller add the following method

```rb
def perform_stripe_payment
  order = Order.find(params[:id])
  customer = Stripe::Customer.create(
    email: params[:email],
    source: params[:stripeToken],
    description: 'slowfood client'
  )

charge = Stripe::Charge.create(
  customer: customer.id,
  amount: order.order_total,
  currency: 'sek'
)
  charge
end
```

We are calling the Stripe gem to create a customer but also to make charge the customer.

You will be getting errors now, we need to fix that by adding the stripe gem to our Gemfile.

`gem 'stripe-rails'`

Run the tests again and your new error message should something in the lines of

```bash
Stripe::AuthenticationError:
      No API key provided. Set your API key using "Stripe.api_key = <API-KEY>". You can generate API keys from the Stripe web interface. See https://stripe.com/api for details, or email support@stripe.com if you have any questions.
```

This means that we need to use our API key that Stripe has provided for us.
