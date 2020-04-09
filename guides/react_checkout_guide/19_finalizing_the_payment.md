## Finalizing the payment

Next up we want to modify the test with the mocked data.

```rb
before do
  put "/api/orders/#{order.id}",
      params: { activity: "finalize",
                stripeToken: StripeMock.create_test_helper.generate_card_token,
                email:'user@mail.com'}
end
```

And finally update the perform_stripe_payment method in our controller.

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
  amount: order.order_total.to_i * 100,
  currency: 'sek'
)
  charge.paid
end
```

Run your tests now and they should be going green. So Stripe added.
Let's celebrate by making a commit!
