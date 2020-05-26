# API part

```rb
# Gemfile

gem 'stripe-rails'

group :development, :test do
  gem 'stripe-ruby-mock'
end

```

## Test

```rb
# spec/requests/api/user_can_purchase_a_subscription_spec.rb

require "stripe_mock"

RSpec.describe "POST api/subscriptions" do
  let!(:stripe_helper) { StripeMock.create_test_helper }
  before(:each) { StripeMock.start }
  after(:each) { StripeMock.stop }

  let(:card_token) { stripe_helper.generate_card_token }
  let(:invalid_token) { "123456789" }

  let(:product) { stripe_helper.create_product }
  let!(:plan) {
    stripe_helper.create_plan(
      id: "platinum_plan",
      amount: 1000000,
      currency: "usd",
      interval: "month",
      interval_count: 12,
      name: "Berlingo News Premium Platinum Plan",
      product: product.id,
    )
  }

  let(:user) { create(:user) }
  let(:user_credentials) { user.create_new_auth_token }
  let(:headers) { { HTTP_ACCEPT: "application/json" }.merge!(user_credentials) }

  describe "with valid stripe token" do
    describe "successfully" do
      before do
        post "/api/subscriptions",
             params: {
               stripeToken: card_token,
             },
             headers: headers
        user.reload
      end

      it "with valid stripe token recieve successful response" do
        expect(response).to have_http_status 200
      end

      it "receives success message" do
        expect(response_json["message"]).to eq "Transaction cleared"
      end

      it "has their status updated to premium" do
        expect(user.premium_user).to eq true
      end
    end
  end

  describe "unsuccessfully" do
    describe "with invalid stripe token" do
      before do
        post "/api/subscriptions",
             params: {
               stripeToken: invalid_token,
             },
             headers: headers
      end

      it "recieves message 'Transaction rejected, token invalid'" do
        expect(response_json["error_message"]).to eq "Invalid token id: 123456789"
      end

      it "recieve error response" do
        expect(response).to have_http_status 400
      end

      it "has their status remain unchanged" do
        expect(user.premium_user).to eq false
      end
    end

    describe "with no stripe token" do
      before do
        post "/api/subscriptions",
             headers: headers
      end

      it "recieves 'No Stripe token detected'" do
        expect(response_json["error_message"]).to eq "Internal problem with your payment, please contact our customer support"
      end

      it "recieve error response" do
        expect(response).to have_http_status 400
      end
    end

    describe "when user is not signed in" do
      before do
        post "/api/subscriptions",
          params: {
            stripeToken: card_token,
          }
      end

      it "returns unsuccessful response" do
        expect(response).to have_http_status 401
      end

      it "returns message to sign in or register first" do
        expect(response_json["errors"][0]).to eq "You need to sign in or sign up before continuing."
      end
    end

    describe "when stripe declines subscription for user" do
      before do
        custom_error = StandardError.new("Subscription cannot be created")

        StripeMock.prepare_error(custom_error, :create_subscription)

        post "/api/subscriptions",
          params: {
            stripeToken: card_token,
          },
          headers: headers
      end

      it "returns unsuccessful response" do
        expect(response).to have_http_status 400
      end

      it "returns error message" do
        expect(response_json["error_message"]).to eq "Subscription cannot be created"
      end
    end
  end
end
```

## Implementation code

Before you do this you need to go to stripe and define ur subscription plan and get ur access keys.

```rb
# config/application.rb

module Newsroom1Api
  class Application < Rails::Application
    # other code

    config.stripe.publishable_key = Rails.application.credentials.stripe[:publishable_key]
    config.stripe.secret_key = Rails.application.credentials.stripe[:secret_key]
    
    config.generators do |generate|
      # other code
    end
  end
end
```

```rb
# config/stripe/plans.rb 

# This file contains descriptions of all your stripe plans

# Example
# Stripe::Plans::PRIMO #=> 'primo'

Stripe.plan :platinum_plan do |plan|
  # plan name as it will appear on credit card statements
  plan.name = 'Subscription Plan'

  # amount in cents.
  plan.amount = 1000000

  # currency to use for the plan (default '
  usd')
  plan.currency = 'usd'

  # interval must be either 'day', 'week', 'month' or 'year'
  plan.interval = 'month'

  # only bill once every three months (default 1)
  plan.interval_count = 12

  # number of days before charging customer's card (default 0)
  plan.trial_period_days = 30
end

# Once you have your plans defined, you can run
#
#   rake stripe:prepare
#
# This will export any new plans to stripe.com so that you can
# begin using them in your API calls.
```

```rb
# config/routes.rb

Rails.application.routes.draw do
  mount_devise_token_auth_for 'User', at: 'api/auth', skip: [:omniauth_callbacks]
  namespace :api do 
    # other code
    resources :subscriptions, only: [:create], constraints: { format: 'json'}
  end
end
```

```rb
# app/controllers/api/subscriptions_controller.rb

class Api::SubscriptionsController < ApplicationController
  before_action :authenticate_user!

  def create
    if params[:stripeToken]
      begin
        customer = Stripe::Customer.list(email: current_user.email).data.first
        customer = Stripe::Customer.create({ email: current_user.email, source: params[:stripeToken] }) unless customer
        subscription = Stripe::Subscription.create({ customer: customer.id, plan: "platinum_plan" })

        if Rails.env.test?
          invoice = Stripe::Invoice.create({
            customer: customer.id,
            subscription: subscription.id,
            paid: true,
          })

          subscription.latest_invoice = invoice.id
        end

        status = Stripe::Invoice.retrieve(subscription.latest_invoice).paid

        if status
          current_user.premium_user = true
          current_user.save
          render json: { message: "Transaction cleared" }
        else
          stripe_error_handler("Transaction did not go through")
        end
      rescue => error
        stripe_error_handler(error.message)
      end
    else
      stripe_error_handler("Internal problem with your payment, please contact our customer support")
    end
  end

  private

  def stripe_error_handler(error)
    render json: { error_message: error }, status: 400
  end
end
```

I am not sure that we need this file. If you run in to problems for some reason, add it.
```rb
# config/stripe/coupons.rb

# This file contains descriptions of all your statically defined
# stripe coupons. You may wish to define unique one-off coupons
# elsewhere, but for ones you will use many times, and will be
# shared between users, this is a good place.

# Example
# Stripe::Coupons::Gold25 #=> 'gold25'

# Stripe.coupon :gold25 do |coupon|
#   # specify if this coupon is useable 'once', 'forever', or 'repeating'
#   coupon.duration = 'repeating'
#
#   # absolute amount, in cents, to discount
#   coupon.amount_off = 199
#
#   # what currency to interpret the coupon amount
#   coupon.currency = 'usd'
#
#   # how long will this coupon last? (only valid for duration of 'repeating')
#   coupon.duration_in_months = 6
#
#   # percentage off
#   coupon.percent_off = 25
#
#   UTC timestamp specifying the last time at which the coupon can be redeemed
#   coupon.redeem_by = (Time.now + 15.days).utc
#
#   # How many times can this coupon be redeemed?
#   coupon.max_redemptions = 10
# end
#
# Once you have your coupons defined, you can run
#
#   rake stripe:prepare
#
# This will export any new coupons to stripe.com so that you can
# begin using them in your API calls. Any coupons found that are not in this
# file will be left as-is.
```

# Client part

## Test
```js
// cypress/integration/registeredUserCanPurchaseASubscription.feature.js

describe("registered user can purchase a subscription", () => {
  beforeEach(() => {
    cy.server();
    // for index action
    cy.route({
      method: "GET",
      url: "**/articles",
      response: "fixture:articles_list_response.json"
    });
   
    // for payment response
    cy.route({
      method: "POST",
      url: "**/subscriptions",
      response: { status: "paid" }
    });

    // for login with j-tockauth
    cy.route({
      method: "POST",
      url: "**/**",
      response: "fixture:login.json"
    });

    // for validate token request that j-tockauth makes automatically
    cy.route({
      method: "GET",
      url: "**/auth/**",
      response: "fixture:login.json"
    });
    cy.visit("/");

    // login the user
    cy.get("#login").click();

    cy.get("#login-form").within(() => {
      cy.get("#email").type("user@mail.com");
      cy.get("#password").type("password");
      cy.get("#login-button")
        .contains("Login")
        .click();
    });
  });

  it("by clicking buy subscription", () => {
    // here we assume that there is a button in the header or something that will be
    // visible after logging in
    cy.get("button")
      .contains("Buy Subscription")
      .click();
    cy.wait(1000);

    // this can be removed but could be good to check that the form has rendered during development
    // due to the <Elements /> can be tricky during the implementation of stripe
    cy.get("form[id='payment-form']").should("be.visible");

    // the "typeInStripeElement" command is defined in the commands.js file, the next snippet displays this
    cy.typeInStripeElement("cardnumber", "4242424242424242");
    cy.typeInStripeElement("exp-date", "0425");
    cy.typeInStripeElement("cvc", "575");
    cy.get("button")
      .contains("Submit Payment")
      .click();

    // this is just an example of how we might show off that the user had a successfull transaction
    cy.get("#subscription-message").should(
      "contain",
      "You are now a Premium Platinum member!"
    );
  });
});
```

```js
// cypress/support/commands.js

Cypress.Commands.add('typeInStripeElement', (element, value) => {
  cy.get(`#${element} div iframe`)
    .then($iframe => {
      const $body = $iframe.contents().find("body");
      cy.wrap($body)
        .find(`input[name^="${element}"]`)
        .type(value, { delay: 10 });
    });
})
```


## Implementation code
```bash
$  yarn add react-stripe-elements
```

This depends on what component will render the `SubscriptionForm` component.
```js
import React from "react";
import { Switch, Route } from "react-router-dom";
import ArticleList from "./components/ArticleList";
import Header from "./components/Header";
import SingleArticle from "./components/SingleArticle";
import SubscriptionForm from "./components/SubscriptionForm";
import Navbar from "./components/Navbar";

// !!!
import { Elements } from "react-stripe-elements";
// !!!

const App = () => {
  return (
    <>
      <Header />
      <Navbar />
      <Switch>
        <Route exact path="/" component={ArticleList}></Route>
        <Route exact path="/article/:id" component={SingleArticle}></Route>
        <Route exact path="/category/:category" component={ArticleList}></Route>
        <Elements>
          <Route exact path="/subscription" component={SubscriptionForm}></Route>
        </Elements>
      </Switch>
    </>
  );
}
export default App;
```

```js
// src/components/SubscriptionForm.jsx

import React from "react";
import { Header, Form, Button, Segment } from "semantic-ui-react";
import {
  CardNumberElement,
  CardExpiryElement,
  CardCVCElement,
  injectStripe
} from "react-stripe-elements";
import axios from "axios";
import { useDispatch, useSelector } from "react-redux";

const SubscriptionForm = props => {
  const dispatch = useDispatch();
  const userEmail = useSelector(state => state.userEmail);
  const errorMessage = useSelector(state => state.errorMessage);
  let headers = JSON.parse(localStorage.getItem("J-tockAuth-Storage"));

  const submitPayment = async event => {
    event.preventDefault();
    const response = await props.stripe.createToken()

    try {
      let paymentStatus = await axios.post(
        "/subscriptions",
        {
          stripeToken: response.token.id,
          email: userEmail
        },
        { headers: headers }
      );

      if (paymentStatus.status === 200)
        dispatch({
          type: "FLASH_MESSAGE",
          payload: {
            flashMessage: `${t("subscription.flash-message")}`,
            showArticlesList: true,
            showSubscription: false,
            premiumUser: true
          }
        });
      } catch (error) {
         dispatch({
          type: "ERROR_MESSAGE",
          payload: { errorMessage: response.message }
        });
      }
    }
  };

  return (
    <>
      <div id="payment">
        <form id="payment-form">
          <h2>
            Payment Form
          </h2>
          <h5>
            Step above the crowd with our Premium Platinum Plan for only 10,000SEK per year.
          </h5>
          <h5>
            This yearly subscription will allow you to access all the amazing ultra premium content in addition to our free content.
          </h5>

          <div id="card-info">
            <label>Card Number: </label>
            <CardNumberElement id="cardnumber" />
            <label>"Expiry Date: </label>
            <CardExpiryElement id="exp-date" />
            <label>CVC: </label>
            <CardCVCElement id="cvc" />
            <button
              onClick={event => {
                submitPayment(event);
              }}
            >
              Submit Payment
            </button>
          </div>
        </form>
        {errorMessage}
      </div>
    </>
  );
};
export default injectStripe(SubscriptionForm);
```

