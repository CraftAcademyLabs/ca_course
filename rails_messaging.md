# Rails Messaging

####Objective

We want to create a simple application that lets logged in users send and receive messages privately between themselves. Our application should keep a list of messages organized into folders: sentbox, inbox and trash and also ability to send notifications via mail.

Lets start out by creating a new rails app, lets name it messenger

```
$ rails new rails_messaging
```
We'll be using **[Bootstrap](http://getbootstrap.com/)** css under the hood for some quick styling. Next up, we will need to set up and authentication mechanism, we'll use **[Devise](https://github.com/plataformatec/devise)** for that. Let's also throw in the **[Mailboxer](https://github.com/mailboxer/mailboxer)** gem at this point and leave it to simmer for a while while we work on authentication.

```ruby
gem 'bootstrap-sass'
gem 'devise'
gem 'mailboxer'
```
We then install our gems using the bundle command
```
$ bundle install
```
AUTHENTICATION

We now need to run Devise's generator to install an initializer that will describe all options available to configure Devise.
```
$ rails generate devise:install
```
Completing the additional steps as described on the post-install messages that appear after running the above generator, lets create a controller and name it welcome with one action.
```
$ rails g controller welcome index
```
Lets make the root path of our application point to the index action of our newly created welcome controller
```ruby
# config/routes.rb

Rails.application.routes.draw do
  root 'welcome#index'
end
```
We need to tweak the application.html layout file a bit to add the flash messages

```erb
# app/views/layoits/application.html.erb

<!DOCTYPE html>
<html>
<head>
  <title>Messenger</title>
  <%= stylesheet_link_tag 'application', media: 'all', 'data-turbolinks-track' => true %>
  <%= stylesheet_link_tag "//maxcdn.bootstrapcdn.com/font-awesome/4.3.0/css/font-awesome.min.css" %>
  <%= javascript_include_tag 'application', 'data-turbolinks-track' => true %>
  <%= csrf_meta_tags %>
</head>
<body>

<%= render 'layouts/nav' %>

<% flash.each do |key, value| %>
    <div class="text-center <%= flash_class(key) %>">
      <%= value %>
    </div>
<% end %>

<div class="container">
  <%= yield %>
</div>


</body>
</html>
```
You'll notice the use of the flash_class function that takes the key as an argument, that's actually a helper method I define in the application_helper.rb in the helpers folder. Used to return the appropriate bootstrap classes based on the flash key.

```ruby
# app/helpers/application_helper.rb

module ApplicationHelper
  def flash_class(level)
    case level.to_sym
      when :notice then "alert alert-success"
      when :info then "alert alert-info"
      when :alert then "alert alert-danger"
      when :warning then "alert alert-warning"
    end
  end
end
````
We'll also add `_nav` partial that holds our applications navigation in the layouts folder.

```erb
#views/layouts/_nav.html.erb

<!-- Fixed navbar -->
<nav class="navbar navbar-inverse navbar-fixed-top">
  <div class="container">
    <div class="navbar-header">
      <button type="button" class="navbar-toggle collapsed" data-toggle="collapse" data-target="#navbar" aria-expanded="false" aria-controls="navbar">
        <span class="sr-only">Toggle navigation</span>
        <span class="icon-bar"></span>
        <span class="icon-bar"></span>
        <span class="icon-bar"></span>
      </button>
      <%= link_to "Messenger", root_path, class: "navbar-brand" %>
    </div>
    <div id="navbar" class="navbar-collapse collapse">
      <ul class="nav navbar-nav pull-right">
        <li class="active"><%= link_to "Login", "#" %></li>
        <li><%= link_to "Sign up", "#" %></li>
      </ul>
    </div><!--/.nav-collapse -->
  </div>
</nav>
````
We also want to copy over the Devise's views to our project so that we can tweak them
```
$ rails generate devise:views
```
Let's create a `User` model configured with Devise modules, Devise has an awesome generator for this:
```
$ rails generate devise User
$ rake db:migrate
```
The generator also configures your `config/routes.rb` file to point to the Devise controller. You can check the User model for any additional configuration options you might want to add but for our case we will leave it at its defaults. At this point we can update the login and sign up links in our _nav partial to point to the appropriate routes.

```erb
#views/layouts/_nav.html.erb

<!-- truncated -->
  <ul class="nav navbar-nav pull-right">
    <% if user_signed_in? %>
        <li><%= link_to "Hello, #{current_user.name}", "#" %></li>
        <li><%= link_to "Logout", destroy_user_session_path, method: :delete %></li>
    <% else %>
        <li class="active"><%= link_to "Login", new_user_session_path %></li>
        <li><%= link_to "Sign up", new_user_registration_path %></li>
    <% end %>
  </ul>
<!-- truncated -->
```
For our application, we want users to provide their names during registration, lets create a migration to add a name attribute to our users.
```
$ rails g migration AddNameToUsers name:string
$ rake db:migrate
```
We then add the name input field to Devise's views
```erb
#app/views/devise/registrations/new.html.erb

<h2>Sign up</h2>

<%= form_for(resource, as: resource_name, url: registration_path(resource_name)) do |f| %>
    <%= devise_error_messages! %>

    <div class="form-group">
      <%= f.label :name %>
      <%= f.text_field :name, class: "form-control" %>
    </div>

     <!-- [.....] -->
    <%= f.submit "Sign up", class: "btn btn-primary" %>
<% end %>
````

```erb
#app/views/devise/registrations/edit.html.erb

<h2>Edit <%= resource_name.to_s.humanize %></h2>

<%= form_for(resource, as: resource_name, url: registration_path(resource_name), html: { method: :put }) do |f| %>
  <%= devise_error_messages! %>

    <div class="form-group">
      <%= f.label :name %>
      <%= f.text_field :name, class: "form-control" %>
    </div>
    <!-- [....] -->

    <%= f.submit "Update", class: "btn btn-primary" %>

<% end %>
<!-- [...] -->
````
Since we are running this app on Rails 4, we will need to whitelist the name parameter. In our application controller file:
```ruby
# app/controllers/application_controller.rb

class ApplicationController < ActionController::Base
  # Prevent CSRF attacks by raising an exception.
  # For APIs, you may want to use :null_session instead.
  protect_from_forgery with: :exception

  before_action :configure_permitted_parameters, if: :devise_controller?

  protected

  def configure_permitted_parameters
    devise_parameter_sanitizer.for(:sign_up) << :name
    devise_parameter_sanitizer.for(:account_update) << :name
  end
end
```
###Mailboxer

We've already added the Mailboxer gem already in our Gemfile. Now we can proceed to install it and update our database.
```
$ rails g mailboxer:install
$ rake db:migrate
```
The generator creates an initializer file mailboxer.rb which you should use this to edit the default mailboxer settings. The options are quite straight forward, tweak them to your liking. We will then change the default name_method since we already have the name attribute in our user model to prevent conflict.
```ruby
#config/initializers/mailboxer.rb

Mailboxer.setup do |config|
  # [...]

  #Configures the methods needed by mailboxer
  config.email_method = :mailboxer_email
  config.name_method = :mailboxer_name

  # [...] 
end
```
### The Model

For us to equip our User model with Mailboxer functionalities we have to add `acts_as_messageable`. This will enable us send user-to-user messages. We also need to add an identity defined by a name and an email in our User model as required by Mailboxer.

Our model must therefore have this specific methods. These methods are: mailboxer_email and mailboxer_name as stated in our mailboxer initializer file. You should make sure to change this when you edit their names in the initializer.
```ruby
#app/models/user.rb

class User < ActiveRecord::Base
  devise :database_authenticatable, :registerable,
         :recoverable, :rememberable, :trackable, :validatable

  acts_as_messageable

  def mailboxer_name
    self.name
  end

  def mailboxer_email(object)
    self.email
  end
end
```
### The Controllers

We will be using two controllers to handle the messaging system.

**MailboxController** - This controller will hold our top-level folders (inbox, sentbox and trash) and be responsible for displaying the messages in each of this folders.
**ConversationsController** - This controller will handle various actions including creating conversations, replies and deletion of messages etc.


Let's start by creating the mailbox controller
```
$ rails g controller mailbox
```
We will then add some custom routes for inbox, sentbox and trash in our routes.rb file
```ruby 
routes.rb (config/routes.rb)

Rails.application.routes.draw do
  devise_for :users
  root 'welcome#index'

  # mailbox folder routes
  get "mailbox/inbox" => "mailbox#inbox", as: :mailbox_inbox
  get "mailbox/sent" => "mailbox#sent", as: :mailbox_sent
  get "mailbox/trash" => "mailbox#trash", as: :mailbox_trash
end
```
Let's define this methods in our mailbox controller
```ruby
#app/controllers/mailbox_controller.rb

class MailboxController < ApplicationController
  before_action :authenticate_user!

  def inbox
    @inbox = mailbox.inbox
    @active = :inbox
  end

  def sent
    @sent = mailbox.sentbox
    @active = :sent
  end

  def trash
    @trash = mailbox.trash
    @active = :trash
  end
end
```
We will use the `@active` variable to highlight the currently selected folder in our view. We can now define the mailbox method as a helper method in our application_controller.rb file.
```
#app/controllers/application_controller.rb

class ApplicationController < ActionController::Base
  # [...]
  helper_method :mailbox
  
  private

  def mailbox
    @mailbox ||= current_user.mailbox
  end

  protected

  # [...]
end
```
Let's now create a view file for each of the actions in the mailbox controller. Each of this will share the same partial to navigate between the mailbox folders
```erb 
#app/views/mailbox/inbox.html.erb

<%= render partial: 'mailbox/folder_view' %>
```
```erb 
#app/views/mailbox/sent.html.erb

<%= render partial: 'mailbox/folder_view' %>
view rawsent.html.erb hosted with ❤ by GitHub
trash.html.erb (app/views/mailbox/trash.html.erb)

<%= render partial: 'mailbox/folder_view' %>
```
Lets create the folder_view partial, in our mailbox folder, create a new file name it `_folder_view.html.erb` and paste the following contents.
```erb
#app/views/mailbox/_folder_view.html.erb

<div class="row">
  <div class="spacer"></div>
  <div class="col-md-12">
    <!-- we'll configure this to compose new conversations later -->
    <%= link_to "Compose", "#", class: "btn btn-success" %>
    <div class="spacer"></div>
  </div>

  <div class="col-md-4">
    <div class="panel panel-default">
      <div class="panel-body">
        <%= render 'mailbox/folders' %>
      </div>
    </div>
  </div>

  <div class="col-md-8">
    <div class="panel panel-default">
      <div class="panel-body">
        <!-- individual conversations will show here -->
      </div>
    </div>
  </div>

</div>
```
Inside here, we also make use of another partial called folders. Lets also, in the same mailbox folder create a new file `_folders.html.erb` and have the following contents
```erb
#app/views/mailbox/_folders.html.erb

<ul class="nav nav-pills nav-stacked">
  <li class="<%= active_page(:inbox) %>">
    <%= link_to mailbox_inbox_path do  %>
        <span class="label label-danger pull-right"><%=unread_messages_count%></span>
        <em class="fa fa-inbox fa-lg"></em>
        <span>Inbox</span>
    <% end %>
  </li>
  <li class="<%= active_page(:sent) %>">
    <%= link_to mailbox_sent_path do %>
        <em class="fa fa-paper-plane-o fa-lg"></em>
        <span>Sent</span>
    <% end %>
  </li>

  <li class="<%= active_page(:trash) %>">
    <%= link_to mailbox_trash_path do %>
        <em class="fa fa-trash-o fa-lg"></em>
        <span>Trash</span>
    <% end %>
  </li>
</ul>
```
Remember the `@active` instance variable we instantiated in our mailbox controller? lets make a helper method that makes use of that variable to highlight the current page. In our `application_helper.rb` file add the following method.
```ruby
#app/helpers/application_helper.rb

module ApplicationHelper
  # [...] 
  def active_page(active_page)
    @active == active_page ? "active" : ""
  end
end
```
For our inbox, it would be a good idea to display the number of unread messages. We will define a helper method in our mailbox_helper.rb file in the helpers directory.
```ruby
#app/helpers/mailbox_helper.rb

module MailboxHelper
  def unread_messages_count
    # how to get the number of unread messages for the current user
    # using mailboxer
    mailbox.inbox(:unread => true).count(:id, :distinct => true)
  end
end
```
We can now add a link in our navigation bar to link to our inbox page
```erb 
#app/views/layous/_nav.html.erb

<!-- [...] -->
    <div id="navbar" class="navbar-collapse collapse">
      <ul class="nav navbar-nav pull-right">
        <% if user_signed_in? %>
            <li><%= link_to "Hello, #{current_user.name}", "#" %></li>
            <li><%= link_to "Inbox", mailbox_inbox_path %></li>
            <li><%= link_to "Logout", destroy_user_session_path, method: :delete %></li>
        <% else %>
            <li class="active"><%= link_to "Login", new_user_session_path %></li>
            <li><%= link_to "Sign up", new_user_registration_path %></li>
        <% end %>
      </ul>
    </div><!--/.nav-collapse -->
<!-- [...] -->
```
Clicking the inbox link should take you to our up and running inbox page with navigation already in place for the inbox, sent and trash folders.



### Conversations

With our mailbox ready to show messages from our inbox, sent and trash folders, its now time we created functionality to create and send new messages to other users. Let's go ahead and create our second controller (conversations)
```
$ rails g controller conversations
```
Let's not forget to add a resources route for our conversations which will also hold other 3 member routes to handle reply, trash and untrashing of messages.
```ruby
config/routes.rb

Rails.application.routes.draw do
  # [...]
  
  # conversations
  resources :conversations do
    member do
      post :reply
      post :trash
      post :untrash
    end
  end
end
```
With the conversations routes in place, lets edit our compose button link in our folder_view partial to point to the new action of our conversations controller.
```erb 
#app/views/mailbox/_folder_view.html.erb

<div class="row">
  <div class="spacer"></div>
  <div class="col-md-12">
    <%= link_to "Compose", new_conversation_path, class: "btn btn-success" %>
    <div class="spacer"></div>
  </div>
  <!-- [...] -->
</div>
```
We now need to create the new method in our conversations controller and its corresponding view file
```ruby
#app/controllers/conversations_controller.rb

class ConversationsController < ApplicationController
  before_action :authenticate_user!

  def new
  end
end
``` 
```erb
#app/views/conversations/new.html.erb

<%= render partial: 'mailbox/folder_view', locals: { is_conversation: true } %>
```
Notice we are still using our folder_view partial which is in our mailbox folder. This partial will be responsible for displaying different contents based on the current view. To start with, we are passing in a locale called is_conversation. When this is set to true, we will render the form to create new conversations and messages, else we will show contents for each mailbox folder.

In our views folder, conversations folder, create a form partial called `_form.html.erb`, that will hold the form to create new conversations.
```erb 
#app/views/conversations/_form.html.erb

<%= form_for :conversation, url: :conversations, html: { class: "" } do |f| %>
    <div class="form-group">
      <%= f.label :recipients %>
      <%= f.select(:recipients, User.all.collect {|p| [ p.name, p.id ] }, {}, { multiple: true , class: "form-control" })%>
    </div>
    <div class="form-group">
      <%= f.label :subject %>
      <%= f.text_field :subject, placeholder: "Subject", class: "form-control" %>
    </div>
    <div class="form-group">
      <%= f.label :message %>
      <%= f.text_area :body, class: 'form-control',placeholder: "Type your message here", rows: 4  %>
    </div>

    <%= f.submit "Send Message", class: "btn btn-success" %>

<% end %>
```
We then need to update our folder_view partial to render our conversation form if the is_conversation variable is set to true.
```erb 
#app/views/mailbox/_folder_view.html.erb

<div class="row">
  <!-- [...] -->
  <div class="col-md-8">
    <div class="panel panel-default">
      <div class="panel-body">
        <% if is_conversation %>
            <%= render 'conversations/form' %>
        <% else %>
        <!-- TODO -->
        <% end %>
      </div>
    </div>
  </div>

</div>
```
Let's not forget to update your mailboxes passing the is_conversation locale as false. Update your mailbox view files (`inbox.html.erb`,`sent.html.erb`,`trash.html.erb`) to read
```erb
`inbox.html.erb`,`sent.html.erb`,`trash.html.erb` (in the `app/views/mailbox/`folder )

<%= render partial: 'mailbox/folder_view', locals: { is_conversation: false } %>
```
With this, clicking on the Compose button on either page should take you to the new view of our conversations controller and you should have a beautiful form ready to send messages



With our form up and running, we need to implement the create action in our conversations controller to save the messages to the database. We call Mailboxer's send_message method on the current user and that takes in an array of recipients, the message body and message subject after which we redirect to the show action of our conversations controller.
```ruby 
#app/controllers/conversations_controller.rb

class ConversationsController < ApplicationController
  before_action :authenticate_user!

  def new
  end

  def create
    recipients = User.where(id: conversation_params[:recipients])
    conversation = current_user.send_message(recipients, conversation_params[:body], conversation_params[:subject]).conversation
    flash[:success] = "Your message was successfully sent!"
    redirect_to conversation_path(conversation)
  end

  def show
    @receipts = conversation.receipts_for(current_user)
    # mark conversation as read
    conversation.mark_as_read(current_user)
  end


  private

  def conversation_params
    params.require(:conversation).permit(:subject, :body,recipients:[])
  end
end
```
As you can see in the show action, we query all messages in the current conversation for the current user and store that in the @reciepts variable. Also, the conversation is actually a helper method we need to define in our application controller to help us get the current conversation.
```ruby
#app/controllers/application_controller.rb

class ApplicationController < ActionController::Base
  # [...]
  helper_method :mailbox, :conversation

  private
  # [...]
  def conversation
    @conversation ||= mailbox.conversations.find(params[:id])
  end

  protected
  # [...]
end
```
Creating the show view of the show action:
```erb
#app/views/conversations/show.html.erb

<div class="row">
  <div class="spacer"></div>
  <div class="col-md-12">
    <%= link_to "Compose", new_conversation_path, class: "btn btn-success" %>
    <div class="spacer"></div>
  </div>

  <div class="col-md-4">
    <div class="panel panel-default">
      <div class="panel-body">
        <%= render 'mailbox/folders' %>
      </div>
    </div>
  </div>

  <div class="col-md-8">
    <div class="panel panel-default">
      <div class="panel-body">
        <%= render partial: 'messages' %>
      </div>
    </div>
  </div>

</div>
``` 
Notice we call another partial messages in our new view. Create a new partial `_messages` in the `conversations` folder.
```erb
#app/views/conversations/_messages.html.erb

<% @receipts.each do |receipt| %>
    <% message = receipt.message %>
    <div class="media">
      <div class="media-left">
        <!-- user avators can go here -->
        <a href="#">
          <img class="media-object" src="http://placehold.it/64x64" alt="...">
        </a>
      </div>
      <div class="media-body">
        <h4 class="media-heading">
          <%= message.sender.name %> <br>
          <small><b>Subject: </b><%= message.subject %></small><br>
          <small><b>Date: </b><%=  message.created_at.strftime("%A, %b %d, %Y at %I:%M%p") %></small>
        </h4>
        <%= message.body %>
      </div>
    </div>
<% end %>
```
Creating a new conversation should successfully redirect you to that specific conversation and you should have your beautiful conversation show page like the one below



Now that we can successfully send a message, we need to mechanism to reply, right? We  need to add a reply form on this page and a subsequent reply action in our conversations controller.
```erb 
#app/views/conversations/show.html.erb

<div class="row">
   <!--[...]-->

  <div class="col-md-8">
    <div class="panel panel-default">
      <div class="panel-body">
        <%= render partial: 'messages' %>
      </div>
      <div class="panel-footer">
        <!-- Reply Form -->
        <%= form_for :message, url: reply_conversation_path(conversation) do |f| %>
            <div class="form-group">
              <%= f.text_area :body, placeholder: "Reply Message", rows: 4, class: "form-control" %>
            </div>
            <%= f.submit "Reply", class: 'btn btn-danger pull-right' %>
        <% end %>
        <div class="clearfix"></div>
      </div>
    </div>
  </div>

</div>
```
view rawconversations_show_1.html.erb hosted with ❤ by GitHub
Now lets add the reply action to our controller
```ruby 
#app/controllers/conversations_controller.rb

class ConversationsController < ApplicationController
  before_action :authenticate_user!
  # [...]

  def reply
    current_user.reply_to_conversation(conversation, message_params[:body])
    flash[:notice] = "Your reply message was successfully sent!"
    redirect_to conversation_path(conversation)
  end

  private
  # [...]

  def message_params
    params.require(:message).permit(:body, :subject)
  end
end
```
Mailboxer's reply_to_conversation method makes replying to conversations a breeze. It takes in a conversation and the message body and optionally a subject as arguments. If you want users to change the subject during reply, then remember to add the subject text field in the reply form and also in the reply_to_conversation method above as the third argument.

DISPLAYING MESSAGES IN OUR MAILBOX FOLDERS

Our mailbox controller views are lonely and we need to show contents in each of them from which users can click on a snippet and view the full message. In this folders, we want to show a snippet of the last message in each unique conversation.

Lets create a new partial conversation inside our conversations folder name it `_conversation.html.erb`
```erb
#app/views/conversations/_conversation.html.erb

<div class="media">
  <div class="media-left">
    <a href="#">
      <img class="media-object" src="http://placehold.it/64x64" alt="...">
    </a>
  </div>
  <div class="media-body">
    <h4 class="media-heading">
      <%= conversation.originator.name %> <br>
      <small><b>Subject: </b><%= conversation.subject %></small><br>
      <small><b>Date: </b><%=  conversation.messages.last.created_at.strftime("%A, %b %d, %Y at %I:%M%p") %></small>
    </h4>
    <%= truncate conversation.messages.last.body, length: 145 %>
    <%= link_to "View", conversation_path(conversation)  %>
  </div>
</div>
```
Updating our inbox, sent and trash views
```erb
#app/views/inbox.html.erb

<%= render partial: 'mailbox/folder_view', locals: { is_conversation: false, messages: @inbox } %>
```
```erb
#app/views/sent.html.erb

<%= render partial: 'mailbox/folder_view', locals: { is_conversation: false, messages: @sent } %>
``` 
```erb
#app/views/trash.html.erb

<%= render partial: 'mailbox/folder_view', locals: { is_conversation: false, messages: @trash } %>
```
In all of our three views, we pass in messages as a locale to our folder_view partial which represents messages from either our inbox, sentbox or trash. Finally lets update our folder_view partial to use the messages locale and consequently render the conversation partial we just created.
```erb
#app/views/mailbox/_folder_view.html.erb

<div class="row">
 <!--[...]-->
  <div class="col-md-8">
    <div class="panel panel-default">
      <div class="panel-body">
        <% if is_conversation %>
            <%= render 'conversations/form' %>
        <% else %>
            <%= render partial: 'conversations/conversation', collection: messages %>
        <% end %>
      </div>
    </div>
  </div>
</div>
```
You should now be able to see your messages, if any, when you click the inbox and sent links in any view. One last part is remaining, as you can guess, that is ability to delete messages and send them to the trash and also ability to untrash messages which we might have deleted by mistake.

### Deleating and undeleating messages

We will now need to add a "Move to trash" button in each conversation that is not yet deleted. For those already deleted, we need to show an "Untrash" button.
```erb
#app/views/conversations/_conversation.html.erb

<div class="row">
  <div class="spacer"></div>
  <div class="col-md-6">
    <%= link_to "Compose", new_conversation_path, class: "btn btn-success" %>
  </div>
  <div class="col-md-6 text-right">
    <% if conversation.is_trashed?(current_user) %>
        <%= link_to 'Untrash', untrash_conversation_path(conversation), class: 'btn btn-info', method: :post %>
    <% else %>
        <%= link_to 'Move to trash', trash_conversation_path(conversation), class: 'btn btn-danger', method: :post,
                    data: {confirm: 'Are you sure?'} %>
    <% end %>
  </div>
</div>

<div class="row">
  <div class="spacer"></div>
  <div class="col-md-4">
    <div class="panel panel-default">
      <div class="panel-body">
        <%= render 'mailbox/folders' %>
      </div>
    </div>
  </div>

  <div class="col-md-8">
    <div class="panel panel-default">
      <div class="panel-body">
        <%= render partial: 'messages' %>
      </div>
      <div class="panel-footer">
        <!-- Reply Form -->
        <%= form_for :message, url: reply_conversation_path(conversation) do |f| %>
            <div class="form-group">
              <%= f.text_area :body, placeholder: "Reply Message", rows: 4, class: "form-control" %>
            </div>
            <%= f.submit "Reply", class: 'btn btn-danger pull-right' %>
        <% end %>
        <div class="clearfix"></div>
      </div>
    </div>
  </div>

</div>
```
We now add the corresponding trash and untrash methods in our controller
```ruby
#app/controllers/conversations_controller.rb

class ConversationsController < ApplicationController
  before_action :authenticate_user!
  # [...]
  
  def trash
    conversation.move_to_trash(current_user)
    redirect_to mailbox_inbox_path
  end

  def untrash
    conversation.untrash(current_user)
    redirect_to mailbox_inbox_path
  end

  private
  # [..]
end
```
As you can see Mailboxer provides handful methods move_to_trash to send messages to the current users's trash box and untrash to move back the message back to the users inbox.

### Additional features

As you many have noted, the current method of selecting recipients is not very efficient especially when the number of users increase, finding a user can become way too tedious. We can improve on this by use of a jQuery plugin called Chosen which makes it dead simple to select from a large list and is more user-friendly. Fortunately for us, there is a chosen-rails gem that makes integrating this plugin into Rails app very easy.

To Install the gem in our rails app, add chosen-rails to your Gemfile and run bundle install
```ruby
#Gemfile

# [...]
gem 'chosen-rails'
```
We then need to add Chosen to application.js and application.css.scss files:
```javascript
# app/assets/javascripts/application.js

# [....]

//= require jquery
//= require jquery_ujs
//= require chosen-jquery
//= require turbolinks
//= require_tree .
```

```css
# app/assets/stylesheets/application.css

 /* [...] */
 *
 *= require_tree .
 *= require chosen
 *= require_self
 */

@import 'bootstrap';
@import 'bootstrap/theme';
```
We then add the chosen-select class to our select dropdown in our conversation's form select field
```erb
app/views/conversations/_form.html.erb

<%= form_for :conversation, url: :conversations, html: { class: "" } do |f| %>
    <div class="form-group">
      <%= f.label :recipients %>
      <%= f.select(:recipients, User.all.collect {|p| [ p.name, p.id ] }, {}, { multiple: true , class: "chosen-select form-control" })%>
    </div>
    <!--[...]-->
    <%= f.submit "Send Message", class: "btn btn-success" %>

<% end %>
```
Lastly, lets equip our multiple-select dropdown with some Chosen magic. As we will be using plain jquery, rename conversations.js.coffee to conversations.js and paste in the following code
```javascript
conversations.js (app/assets/conversations.js)

var ready;

ready = function(){
    // enable chosen js
    $('.chosen-select').chosen({
        no_results_text: 'No results matched'
    });
}

$(document).ready(ready);
// if using turbolinks
$(document).on("page:load",ready);
```
Reload the page with your form and you should see jQuery Chosen in action.



###Wrap up

That wraps it up for this tutorial and I hope I did a good job of trying to explain the nitty-gritty of implementing a private messaging system using the Mailboxer Gem.  Happy Coding!!

