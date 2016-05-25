## Tips and Tricks

###Routes
When writing your acceptance tests you should use the router helper methods made available by Rails.

Instead of writing
```ruby
visit '/'
```
You can do:
```ruby
visit root_path
```
 
How to find all the different paths, you ask? :wink:. There is a rake task you can use. 
Just run this in your terminal.
```shell
$ rake routes
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
Or if you want to go to that users inbox:
```ruby
visit mailbox_inbox_path
```
In this case you probably don’t have to pass in the `@user` since we have a method in our ApplicationController setting `mailbox` to the `current_user.mailbox`.

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

