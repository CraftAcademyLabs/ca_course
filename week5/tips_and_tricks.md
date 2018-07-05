## Tips and Tricks

###Routes
When writing your acceptance tests you should use the `Rails.application.routes` helper methods.

Instead of writing
```ruby
visit '/'
```
You can do:
```ruby
visit root_path
```
 
How to find all the different paths, you ask? There's a `rails` terminal command  you can use. Just run this in your terminal.
```shell
$ rails routes
                  Prefix Verb   URI Pattern                          Controller#Action
        new_user_session GET    /users/sign_in(.:format)             devise/sessions#new
            user_session POST   /users/sign_in(.:format)             devise/sessions#create
    destroy_user_session DELETE /users/sign_out(.:format)            devise/sessions#destroy
           user_password POST   /users/password(.:format)            devise/passwords#create
       new_user_password GET    /users/password/new(.:format)        devise/passwords#new
      edit_user_password GET    /users/password/edit(.:format)       devise/passwords#edit
                         PATCH  /users/password(.:format)            devise/passwords#update
                         PUT    /users/password(.:format)            devise/passwords#update
cancel_user_registration GET    /users/cancel(.:format)              devise/registrations#cancel
       user_registration POST   /users(.:format)                     devise/registrations#create
   new_user_registration GET    /users/sign_up(.:format)             devise/registrations#new
  edit_user_registration GET    /users/edit(.:format)                devise/registrations#edit
                         PATCH  /users(.:format)                     devise/registrations#update
                         PUT    /users(.:format)                     devise/registrations#update
                         DELETE /users(.:format)                     devise/registrations#destroy
           welcome_index GET    /welcome/index(.:format)             welcome#index
                    root GET    /                                    welcome#index
           mailbox_inbox GET    /mailbox/inbox(.:format)             mailbox#inbox
            mailbox_sent GET    /mailbox/sent(.:format)              mailbox#sent
           mailbox_trash GET    /mailbox/trash(.:format)             mailbox#trash
      reply_conversation POST   /conversations/:id/reply(.:format)   conversations#reply
      trash_conversation POST   /conversations/:id/trash(.:format)   conversations#trash
    untrash_conversation POST   /conversations/:id/untrash(.:format) conversations#untrash
           conversations GET    /conversations(.:format)             conversations#index
                         POST   /conversations(.:format)             conversations#create
        new_conversation GET    /conversations/new(.:format)         conversations#new
       edit_conversation GET    /conversations/:id/edit(.:format)    conversations#edit
            conversation GET    /conversations/:id(.:format)         conversations#show
                         PATCH  /conversations/:id(.:format)         conversations#update
                         PUT    /conversations/:id(.:format)         conversations#update
                         DELETE /conversations/:id(.:format)         conversations#destroy
```

Now, let’s say that you have a user object stored in `@user` and want to access the edit user view in your test. In this case you can tell capybara to go to that edit page with:
```ruby
visit edit_user_registration_path(@user)
```
Or if you want to go to that user's inbox:
```ruby
visit mailbox_inbox_path
```
In this case you probably don’t have to pass in the `@user` since we have a method in our ApplicationController, setting `mailbox` to the `current_user.mailbox`.

!FILENAME app/controllers/application_controller.rb
```ruby
def mailbox
  @mailbox ||= current_user.mailbox
end
```
`current_user` being a Devise method of course.
###Warden helpers

The gem we are using for user authentication, Devise, is build upon Warden that you might remember from the Slow Food challenge. 

Using Warden/Devise methods to log in a user:
Create a new file in the `features/support` folder.

!FILENAME features/support/warden.rb
```ruby
Warden.test_mode!
World Warden::Test::Helpers
After { Warden.test_reset! }
```

Now you can create a step def that look something like this:
```ruby
Given(/^I am logged in as "([^"]*)"$/) do |name|
  user = User.find_by(name: name)
  login_as(user, scope: :user)
end
```
With this method you are NOT required to hardcode and use the users password.
You need to understand that the controller action is not being hit with this way of logging in the user so in order to go to a specific page you will need to add a separate step. Like..
```gherkin
And I am on the "inbox page"
```
...or something.

For logging out the user you can define a step def like this:
```ruby
Given /^I log out$/ do
  logout
end
```
 
Much cleaner and DRY. 

###Testing JavaScript

Add both Selenium and Chromedriver Helper to your `Gemfile`.

!FILENAME Gemfile
```ruby
group :development, :test do
  gem 'chromedriver-helper'
  gem 'selenium-webdriver'
end
```
In your `features/support/env.rb` add configuration for `Chromedriver` and point `Capybara` to use it whenever you want to test features that require you to have JavaScript enabled.

!FILENAME features/support/env.rb
```ruby
Chromedriver.set_version '2.33'

Capybara.register_driver :selenium do |app|
  options = Selenium::WebDriver::Chrome::Options.new(
      args: %w( headless disable-popup-blocking disable-infobars)
  )

  Capybara::Selenium::Driver.new(
      app,
      browser: :chrome,
      options: options
  )
end
Capybara.javascript_driver = :selenium

```

With this setup you have access to a full set of [Selenium and Ruby bindings](https://github.com/SeleniumHQ/selenium/wiki/Ruby-Bindings) you can use in your step definitions. To activate the JavaScript driver on a scenario you have to tag your scenario with `@javascript`.

```gherkin
@javascript
Scenario: Deleting a message
  Given I am logged-in as "Daniel"
  And I send a mail to "Jenny"
  And I am on the "home page"
  ...
```

IF you would like to test all your features using Selenium and Chromedriver, you can add the following setup to your `support/env.rb` file:
```ruby
Capybara.default_driver = :selenium
```
**Note that this will significantly slow down your test suite.**


###Mailboxer methods
You can use methods available by the Mailboxer gem in your step definitions to make them run faster.

Let's say you have a scenario like this:

```gherkin  
Background:
  Given following users exists
    | name   | email             | password |
    | Jenny  | jenny@ranom.com   | password |
    | Daniel | daniel@random.com | password |
      
Scenario: Deleting a message
  Given I am logged in as "Daniel"
  And I send a mail to "Jenny"
  And I am on the "home page"
  And I click on the "Logout" link
  Given I am logged-in as "Jenny"
  And I am on the "home page"
  And I click on the "Inbox" link
  Then I should have "1" messages
  And I click on the "View" link
  And I click on the "Move to trash" link
  Then I should have "0" messages
```

You can use some mailboxer methods
```ruby
Given(/^I am logged in as "([^"]*)"$/) do |name|
  @user = User.find_by(name: name)
  login_as(@user, scope: :user)
end

And(/^I send a mail to "([^"]*)"$/) do |name|
  @receiver = User.find_by(name: name)
  @user.send_message(@receiver, 'Lorem ipsum...', 'Subject')
end


Then(/^I should have "([^"]*)" messages$/) do |expected_count|
  count = @receiver.mailbox.inbox.count
  expect(count).to eq expected_count.to_i
end
```