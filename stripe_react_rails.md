This guide needs to have the order functionality from the previous chapter in place. We will continue on the code both from the backend as well as from the frontend. 

We are going to use stripe to finalize the order by paying for the products. 

Stripe is a payment gateway that is developer-friendly. In this section, we are going to set up the functionality that we need in order to make the payments with debit or credit card. 

First and foremost you need to create an account on stripe. We will get back to this later. But make sure that you have an account ready. So let's set the stage, create a new branch and let's get started. 

As wit everything that we develop here at Craft Academy we work in a test-driven way. This means that we always start with our feature test to stay in scope but also make sure that we are not breaking any functionality that has already been added to the previous features. 

Start by creating a new feature by running the following command in your terminal.
`$ touch cypress/integration/userCanMakePayment.feature.js`

After the file has been created we need to add the following test. 

```js
describe("User can add a product to his/her order", () => {
  beforeEach(() => {
    cy.server();
    cy.route({
      method: "GET",
      url: "http://localhost:3000/api/products",
      response: "fixture:product_data.json"
    });

    cy.route({
      method: "POST",
      url: "http://localhost:3000/api/orders",
      response: "fixture:post_response.json"
    });

    cy.route({
      method: "PUT",
      url: "http://localhost:3000/api/orders/1",
      response: "fixture:put_response.json"
    });

    cy.visit("http://localhost:3001");
    cy.get("#product-2").within(() => {
      cy.get("button")
        .contains("Add to order")
        .click();
    });
    cy.get("#product-3").within(() => {
      cy.get("button")
        .contains("Add to order")
        .click();
    });
    cy.get("button")
      .contains("View order")
      .click();
  });

  it("user can pay for his order", () => {
    cy.get("button")
      .contains("Confirm!")
      .click();
    cy.get("#payment-form").should("exist");
  });
});
```
So what is happening in this test?
Well, we are setting the stage and making sure that we can display a payment form. Remember that we always work in small chunks and that we continually refactor both our test but also our implementation code as we add more and more functionality. 

Let's head over to the component that we want to display our payment form. 

Start by adding a new state, where we are adding the payment form. Why are we setting it to false? 

```js
state = {
  productData: [],
  message: {},
  orderDetails: {},
  showOrder: false,
  orderTotal: "",
  showPaymentform: false
};
```

After that, we need to refactor the button to show the form when the user clicks on it. 


```js
<button onClick={() => this.setState({ showPaymentform: true })}>
  Confirm!
</button>;
{
  this.state.showPaymentform && (
    <div id="payment-form">
      <PaymentForm />
    </div>
  );
}
```
remember to also  import the <PaymentForm/> on the top of the file.
But we don't have a PaymentForm component you are thinking. Well, that's right. We don't so...
Let's create one. 

```bash
$ touch src/components/PaymentForm.jsx
```

```js
import React, { Component } from "react";

class PaymentForm extends Component {
  render() {
    return (
      <div>
        <h1>Here we will show a payment form</h1>
      </div>
    );
  }
}

export default PaymentForm;
```
Run your test, commit and all that fun stuff. 
Just to check, you did remember to create a new branch on git before we started right? Of course you did. silly me....

Let's revisit our test for one moment. When we are dealing with stripe they provide us with a lot of functionality in terms of security and checks. This is good for development but could be an issue when we are trying to test the payment functionality. We need to test that we can see an iframe where we will be displaying the payment form. 

Refactor your test to look like this. 

```js
it("user can pay for his order", () => {
  cy.get("button")
    .contains("Confirm!")
    .click();
  cy.get("#payment-form").should("exist");
  cy.wait(1000);
  cy.get('iframe[name^="__privateStripeFrame5"]').then($iframe => {
    const $body = $iframe.contents().find("body");
    cy.wrap($body)
      .find('input[name="cardnumber"]')
      .type("4242424242424242", { delay: 50 });
  });
});
```

We are finding an input field and filling it out with the card number. Small steps remember. Run your test and behold the error messages. 

Time for the implementation

Add the following package. 

```bash
$ yarn add react-stripe-elements
```

To use stripe we also need to add a script provided by stripe.

Add a script to the head in index.html

```html
<script src="https://js.stripe.com/v3/"></script>
```

And we also need to turn off some of the built-in settings on cypress just to make sure that we are not getting errors because of our browser.

Setting chromeWebSecurity to false in Chrome-based browsers allows you to do the following:

- Display insecure content
- Navigate to any superdomain without cross-origin errors
- Access cross-origin iframes that are embedded in your application

Add `chromeWebSecurity` and set it to `false`

`cypress.json`

```json
{
  "baseUrl": "http://localhost:3001",
  "chromeWebSecurity": false
}
```
Head over to your `index.js` and import the StripeProvider. 

```js
import { StripeProvider } from "react-stripe-elements";
```

And wrap <App> component with the StripeProvider

```js
ReactDOM.render(
  <StripeProvider>
    <App />
  </StripeProvider>,
  document.getElementById("root")
);
```

The StripeProvider gives us access to the Stripe object. The Stripe object will contain the secret, and publishable key which will allow us to access the Stripe API. 

Get your API key from the stripe dashboard, make sure that you select "Show test data" and go to the developer's tab. Copy your Publishable key and add it to the stripe provider in your App component. 

```js
ReactDOM.render(
  <StripeProvider apiKey="put_key_here">
    <App />
  </StripeProvider>,
  document.getElementById("root")
);
```
Time to start working on the payment form itself.
import 

Wrap the PaymentForm component with the Elements, and make sure that you import Elements from `"react-stripe-elements"`

```js
{
  this.state.showPaymentform && (
    <div id="payment-form">
      <Elements>
        <PaymentForm orderDetails={this.state.orderDetails} />
      </Elements>
    </div>
  );
}
```

Move over to your PaymentForm and update it with the following code. 

```js
import React, { Component } from "react";
import { injectStripe, CardNumberElement } from "react-stripe-elements";

class PaymentForm extends Component {
  render() {
    return (
      <>
        <label>Card Number</label>
        <CardNumberElement />
      </>
    );
  }
}

export default injectStripe(PaymentForm);
```

So what are we doing here? We are adding an input field where the users can add their card number but to use that we need to use the injectStripe component to make use of their form elements.  

Run the test now and they should be green. If they do, commit.

So we have added a rudimentary payment form and we have also added the package that we need to use stripe in our react app. But we need multiple input fields for the rest of our card information. So let's add that now. 



We need to update our test again for the remaining input fields, as well as with the assertion that the payment has gone through. 

```js
it("user can pay for his order", () => {
  cy.route({
    method: "PUT",
    url: "http://localhost:3000/api/orders/1",
    body: { activity: "finalize" },
    response: { paid: true, message: "Your order will be ready in 30 minutes!" }
  });
  cy.get("button")
    .contains("Confirm!")
    .click();

  cy.get("#payment-form").should("exist");
  cy.wait(1000);
  cy.get('iframe[name^="__privateStripeFrame5"]').then($iframe => {
    const $body = $iframe.contents().find("body");
    cy.wrap($body)
      .find('input[name="cardnumber"]')
      .type("4242424242424242", { delay: 50 });
  });

  cy.get('iframe[name^="__privateStripeFrame6"]').then($iframe => {
    const $body = $iframe.contents().find("body");
    cy.wrap($body)
      .find('input[name="exp-date"]')
      .type("1222", { delay: 10 });
  });
  cy.get('iframe[name^="__privateStripeFrame7"]').then($iframe => {
    const $body = $iframe.contents().find("body");
    cy.wrap($body)
      .find('input[name="cvc"]')
      .type("999", { delay: 10 });
  });

  cy.get("button")
    .contains("Submit")
    .click();

  cy.get("#payment-form").should("not.exist");
  cy.get(".message").should(
    "contain",
    "Your order will be ready in 30 minutes!"
  );
});
```

Let us go back to your PaymentForm component and update the return with the rest of the payment fields. Make sure that you import the 

```js
		return (
			<>
				<label>Card number </label>
				<CardNumberElement />
				<label>Expiry Date</label>
				<CardExpiryElement />
				<label>CVC</label>
				<CardCVCElement />
				<button>Submit</button>
			</>
```

Next we want to add a function called payWithStripe

```js
async payWithStripe() {
		await this.props.stripe.createToken().then(response => {
			if (response.token) {
				try {
					this.performPayment(response.token)
				}
				catch {

				}
			}

		})
	}
```

We also want to add a function called performPayment. This function will make the payment.

```js
	async performPayment(token) {
		let orderResponse = await axios.put(
			`http://localhost:3000/api/orders/${this.props.orderDetails.id}`,
			{
				activity: 'finalize',
				stripeToken: token
			}
		)
		if (orderResponse.data.paid === true) {
			this.props.finalizeOrder(orderResponse.data.message)

		} else {
			debugger
		}
	}
```

And finally, we need to update the button

```js
<button onClick={this.payWithStripe.bind(this)}>Submit</button>
```

Make sure that you have made adequate imports to the component.

Move over to the DisplayProduct component or your main component where you are adding the products to the order.

Update the Props that you are sending through

```js
<PaymentForm
  orderDetails={this.state.orderDetails}
  finalizeOrder={this.finalizeOrder.bind(this)}
/>
```

And update the finalizeOrder function we are passing in the finializeOrder function as a prop to the component.

```js
	async finalizeOrder(message) {
		let orderTotal = this.state.orderDetails.order_total
		this.setState({ message: { id: 0, message: message }, orderTotal: orderTotal, orderDetails: {}, showPaymentForm: false })
	}
```

Run your tests and they should go green.

Let's move over to the backend 

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

  it '' do
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

Let's fix that. In the private section of the orders controller add the following method

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
    amount: ordr.order_total,
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

Grab the "secret key" from your Stripe dashboard.

Do not reveal this key. To use this securely we will add the key to our credentials file. Credentials is an encrypted file in rails. We need the master.key file in our config folder to open this file. The master.key is added to your gitignore by default so that you don\t accidentally push it to version control. 

Use the following steps to modify your credentials. 

Head over to your terminal and run the following command.

```bash
$ EDITOR='code --wait' rails credentials:edit
```
Notice that your terminal has been locked and the credentials file has been decrypted and opened in your code editor. The terminal will be locked until you save and close the credentials file in your code editor. But before we do that we need to add the secret key and publishable key in this file.


```yml
secret_key_base: 50fba2642c2978bcf7536a53606328149f18c7382a070822bf86f0d141095d9ed2c2c472b77577df08c34c61e36f3a27aaba26fb746cfb6a79ce3456edbcb04b
stripe:
  pk_key: your_publishable_key_here
  secret_key: your_secret_key_here
```

Save and close the file and you should be getting the following message in the terminal.

```bash
16:16 $ EDITOR='code --wait' rails credentials:edit
File encrypted and saved.
```

To make use of the stripe credentials we need to add some configurations.

Head over to the `application.rb` file where we turn off the generators etc and add the following configurations.

```rb
    config.generators do |generate|
      generate.helper false
      generate.assets false
      generate.view_specs false
      generate.helper_specs false
      generate.routing_specs false
      generate.controller_specs false
    end
    config.stripe.secret_key = Rails.application.credentials.stripe[:secret_key]
    config.stripe.publishable_key = Rails.application.credentials.stripe[:pk_key]
  end
```

We do not want to make actual network calls to stripe when we are in development mode. For that we will use a gem called `stripe-ruby-mock`. Make sure that you use the version stated below.

Add the following gem to the Gemfile

`gem 'stripe-ruby-mock', '~> 2.5.6', require: 'stripe_mock'`

Then we want to update the  rails_helper to add the mock functionality

Add `require 'stripe_mock'` to the rails_helper and add the configuration to RSpec

```rb
RSpec.configure do |config|
  config.fixture_path = "#{::Rails.root}/spec/fixtures"
  config.use_transactional_fixtures = true
  config.infer_spec_type_from_file_location!
  config.filter_rails_from_backtrace!
  config.include FactoryBot::Syntax::Methods
  config.include(Shoulda::Matchers::ActiveRecord, type: :model)
  config.before(:each) do
    @stripe_test_helper = StripeMock.create_test_helper
    StripeMock.start
  end
  config.after(:each) do
    StripeMock.stop
  end
end
```
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

  Run your tests now and they should be going green. 
  So Stripe added. 
  Let's celebrate by making a commit!

