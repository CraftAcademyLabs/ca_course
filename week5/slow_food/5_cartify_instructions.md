## Adding checkout functionality with the Cartify Gem

The cartify gem is intended for learning purposes only. This means that the gem will mimic real transactions but not actually make any. The Cartify gem will also add all the different parts of a sales flow from adding a product to a chart to the final checkout togheter with a order confirmation.

### Prerequisites

Before we can implement the cartify gem we need to have two things in order
- We need to have Devise set up with a user model, with the specs
- We need to have a products controller and a products model and test to verify our model
- We need can view products on the landing page
- Cucumber for feature testing

### Setting the stage
As we are going to work in a BDD way the first thing we need to do is to create a new branch and then to create a `feature` file.

```bash
$ git checkout -b visitor_can_add_products_to_cart
$ touch features/visitor_can_add_products_to_cart.feature
```

Next up we are going to add a `Feature definition`, `Background` and a `Scenario` to the feature file.
This feature is not completed and we will continiously refactor it in order to complete the user story.


```gherkin
Feature: Visitor can add products to cart
    As a visitor,
    In order to select products I want to buy
    I would like to be able to add products to an order

Background:
    Given the following products exist
        | name  | description                        | price |
        | Pizza | Crispy, cheesy, round and delicious| 90    |

Scenario: Visitor can add products to cart
    Given I am on the landing page
    And I click on 'Add to cart'
```

Go ahead and run cucumber in the terminal in order to get the step definitions that we need.
Now take the content from the terminal and add the to your step definitions.


### First green tests

Before we do anything else we need to make sure that we have the appropriate factories for both the products model and the user model that we need for our background steps. You should already have created these in previous stories, if not then ow is as a god time as any to do so.


For the products model:
```ruby
FactoryBot.define do
  factory :product do
    name {'Pizza'}
    description {'yummy'}
    price { 10 }
  end
end
```

The first tests:

```ruby
Given("the following products exist") do |table|
    table.hashes.each do |product|
        FactoryBot.create(:product, product)
    end
end

Given("I am on the landing page") do
    visit root_path
end

When("I click on {string}") do |link_name|
    click_on link_name
end
```

The first two test should go green by now, beacuse you have a factory set up and your products are probably on the landing page of the application. If notthen make sure that you set the adjust the test to mirror the path you want to visit.

In order to pass the `And I click on 'Add to cart' on 'Pizza'` step we have to install the Cartify gem.

Make sure that you make a commit before teh next step.

### Adding the Cartify gem

Go to your Gemfile and add
```
gem 'cartify', github: 'CraftAcademy/cartify', branch: 'rails_5_2'
```
Run `bundle`, and then the command `$ rails generate cartify:install --scope all`, if this fails for some reason please run the `scope` options one by one.

Please look at the official documentation for more information.

The Cartify gem requires javascript. Add the `@javascript` tag to the top of the `Feature` file. You need to have Selenium and Webdriver set up in order for the `@javascript` to work. Please refer to the course doucmentation in order to set that up, if do not have that added already.

Cartify adds `resources :products, only: [:show]` to your routes file by default. Please refactor this to suit your application. Another thing that is added is the `mount Cartify::Engine, at: '/'`

_Mount within the Rails routes tells the app that another application exists on that location. Mounting an engine means that the functionality from that engine is available inside your application._

Run `rails routes`in your terminal to make sure that the cartify routes are showing up, if not then pease go over the installation steps again.

The next step is to run the cartify migrations, first we need to add them to our application and then run the migrations:

```bash
$ rails cartify:install:migrations
$ rails db:migrate
```
_Make sure to commit_

Now we need to modify our `user` model with the cartify configurations
_We should have devise and a `user` model already set up as a prerequsitve for the cartify functionality._

Add the following associations to the user model.

`app/models/user.rb`

```ruby
    has_many :orders, class_name: 'Cartify::Order', foreign_key: :user_id
    has_one :billing, class_name: 'Cartify::Billing', foreign_key: :user_id
    has_one :shipping, class_name: 'Cartify::Shipping', foreign_key: :user_id
    has_many :addresses, class_name: 'Cartify::Address', foreign_key: :user_id
```

If we have called our products model for anything else like `Dishes` etc, we need to make adjustments to the cartify initializer file(`config/initializers/cartify.rb`). If not then we can carry on.

The last thing we need to do in order to coplete the configuration is to add the helper methods provided to us by cartify in the application controller.

```ruby
class ApplicationController < ActionController::Base
    helper Cartify::Engine.helpers
    include Cartify::CurrentSession
end
```


### Going back to the Feature test

It's time for us to refactor the feature test a little to fit our needs.
Let's add the assertion step for this feature and refactor the click step:

```gherkin
Scenario: Visitor can add products to cart
    Given I am on the landing page
    And I click on 'Add to cart' on 'Pizza'
    Then I should see '1 item'
```
And the corresponding step definitions:

```ruby
Given("I click on {string} on {string}") do |element, product_name|
    product = Product.find_by_name(product_name)
    within("#product_#{product.id}") do
        click_on element
    end
end


Then("I should see {string}") do |expected_content|
    expect(page).to have_content expected_content
end
```
Please go over the step definition when we are clicking on the product carefully and really understand what is happening.

The next step is to add the `add_to_cart` helper method and the `dom_id` to the `index` view.

```
<table>
  <% @products.each do |product| %>
    <tr id=<%= dom_id(product)%>>
      <td><%= product.name %></td>
      <td><%= product.description %></td>
      <td><%= product.price %></td>
      <td><%= add_to_cart(product, 1, 'Add to cart')%></td>
    </tr>
  <% end %>
</table>
```

If we look at the documentation for the Cartify gem we can see what arguments we need to pass in to the method and how to modify it:
```ruby
add_to_cart(product, quantity, button_name)
# product -     name of your selling product (required!)
# quantity -    how many goods you with put into cart (default: 1)
# button_name - button name (default: "Add to cart")
```
We will use the default values, but remember if you want to change this that you also need to modify your feature file.

The last step is to make sure that we can view the number of products in our cart we have to use the `shop_icon_quantity` helper method.
The best place to add this is in the application.html.erb file before the `yield`

```
<%= shop_icon_quantity %>
```

All the steps should be green after we have added this last step.

Time to submit the PR

