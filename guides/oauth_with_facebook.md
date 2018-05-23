# Login with Facebook for Rails 5

## Introduction
OAuth 2 is an authorization protocol that enables a third-party applications to obtain limited access to an internet service. One of the main aspects of this protocol is the access token that is issued to the application. The token is used by the app to perform various pre-approved actions on the user’s behalf. 

There are numerous of services that support OAuth 2 authentication - in this guide we will focus on setting up the flow of allowing a user to get authenticated using hers/his Facebook account.

The flow that we will be setting up looks something like this: 

* User accesses the application and clicks “Login with Facebook” link
* User is redirected to Facebooks website. The app’s data (client_id) are sent along for identification
* User sees the app’s details (like name, logo, description, etc.) and which actions it would like to perform on his behalf (the scope) 
* If the user trusts the application and is willing to grant it limited (scoped) access to hers/his account, the authorization is approved and the user is redirected back to the application (via a callback URL). Information about the user and a generated token is sent along.
* If the user does not trust the application, she/he simply cancels the authorization. 

## Let's start with an Acceptance Test

We assume that you have a `User` class/model extended with `Devise`. The scenario we want to work with is rather simple:

```gherkin
Feature: User can log in using hers/his Facebook credentials
    As a user of the system
    In order to simplify the sign up/sign in process 
    I would like to be able to authenticate myself using Facebook 


Scenario: Visitor clicks on 'Login with Facebook' and gets authenticated
    Given I visit the site
    And I click "Login with Facebook"
    Then I should be redirected to index page
    And I should see "Successfully authenticated from Facebook account" 
```

Okay, before anything else, let's start to work our way through the interface and tehe error messages we get from cucumber regarding that. 

_**Note: I will leave out the step definitions from this guide and (as well as the setup of Cucumber). If you use RSpec for your acceptance tests (feature specs), you probably know how to translate the above scenario to RSpec**_


Lets modify our layout file (or the view where you would like to display the login link):

```haml
- if user_signed_in?
  = link_to "Logged in as: #{current_user.email}", root_path
  = link_to 'Sign Out', destroy_user_session_path, method: :delete
- else
  = link_to 'Login with Facebook', '#'
```

For now, our facebook login link will not lead anywhere, but rest assured, we will update it soon.

_**Note: You also need to have a section in your code that allows for displaying flash messages. A basic setup would look like this:**_

```haml
%p.notice= notice
%p.alert= alert
```

## Implementation

Let's get started with the actual implementation. There are several things we need to do in order to get this flow working. 
1. Modify you User model and Routes configuration
3. Add a controller that will handle the callbacks
4. Add a mock to avoind hitting the facebook api in test enviromenmt. 
5. Create a Facebook application and get the App Key and Secret
6. Configure Devise Initializer

### Modify you User model and Routes configuration

We will extend the Devise strategy and create 2 new class methods. You will need to modify this setup to suite your own needs, but do take a look at the following code: 

```ruby
class User < ApplicationRecord
  devise :database_authenticatable, :registerable,
         :recoverable, :rememberable, :trackable, :validatable,
         :omniauthable, omniauth_providers: [:facebook]
  has_many :recipes

  def self.new_with_session(params, session)
    super.tap do |user|
      if data = session['devise.facebook_data'] && session['devise.facebook_data']['extra']['raw_info']
        user.email = data['email'] if user.email.blank?
      end
    end
  end

  def self.from_omniauth(auth)
    where(provider: auth.provider, uid: auth.uid).first_or_create do |user|
      user.email = auth.info.email
      user.password = Devise.friendly_token[0,20]
    end
  end
end
```

The key elements here are the `:omniauthable, omniauth_providers: [:facebook]` addition to the `devise` configuration, and the `new_with_session` and `from_omniauth` class methods. 

We will be using our own methods callbacks, so we need to extend the [`Devise::OmniauthCallbacksController`](https://github.com/plataformatec/devise/blob/master/app/controllers/devise/omniauth_callbacks_controller.rb), meaning we need to create our own version and tell Devise to use it. We do that in our `routes.rb` file:

```ruby
Rails.application.routes.draw do
  devise_for :users, controllers: { omniauth_callbacks: :omniauth_callbacks }
end

```

At this point, we can update our `Login with Facebook` link with the actual path we want to use:

```haml
  = link_to 'Login with Facebook', user_facebook_omniauth_authorize_path
```

### Add a controller that will handle the callbacks

Consequently, we need to create that controller and add the functionality to handle the `auth` hash we will get from the service once the user has authorized our application. 

```ruby
class OmniauthCallbacksController < Devise::OmniauthCallbacksController

    def facebook
        @user = User.from_omniauth(request.env['omniauth.auth'])
        if @user.persisted? 
            sign_in_and_redirect @user, event: :authentication
            set_flash_message(:notice, :success, kind: 'Facebook') if is_navigational_format?
        else
            session['devise.facebook_date'] = request.env['omniauth.auth']
            redirect_to new_user_registration_path #or should it be url?
        end
    end

end
```

### Back to the User model for more changes...

If you keep running your tests you'll find out that we are missing some attributes on the user model. We need to add `provider` and `uid` columns to the `users` table in our database. 

```bash 
$ rails g migration AddOmniauthToUsers provider:string uid:string 
```

Remember to run the new migration and re-run your tests. 

### Add a mocked response to avoind hitting the facebook api in test enviromenmt. 

When you run your tests, you'll probably get all kinds of errors. Well, to be honest, this is as far as we can get using acceptance tests for now. There's no way we can interact with the popup window from Facebook (well, that is not entirely true. We could pop that open and perform an actual authorization using Selenium and Chrome driver for example, but we don't want to get into that).

What we want to do is to mock the response and make sure that our tests handle the request locally, rather than trying to access Facebook. 

Let's add a new module in our `features/support` folder. Add a new file called `omni_auth_fixtures.rb` and add the following code: 

```ruby
module OmniAuthFixtures
    def self.facebook_mock
        {provider: 'facebook',
         uid: 10205522242159630,
         info:
             {email: 'thomas@craftacademy.se',
              name: 'Thomas Ochman',
              image: 'http://graph.facebook.com/v2.6/10205522242159630/picture'},
         credentials:
             {token:
                  'EAAXC253O740BANfJYLjz2LCzT1UcfuEsoHpZBMAifdiud8sulF2LIfjDy5BeGiNPEPQjUn7xpvAu0neqnGeoCAvCU2KIucyP5sYNQDaDtCj06UmOF2POEq8ZAajS2zaQ4B7uIIRgv4p3wlACmh9f9MsMnDZB6gZD',
              expires_at: 1517839337,
              expires: true},
         extra:
             {raw_info:
                  {name: 'Thomas Ochman', email: 'thomas@craftacademy.se', id: '10205522242159630'}}}
    
      end
end
```

We also need to tell Cucumber that we want to use the mock in our tests. We can do that by adding a `Before` block in our Cucumber configuration file (`features/support/env.rb`): 

```ruby
Before do 
  OmniAuth.config.test_mode = true
  OmniAuth.config.mock_auth[:facebook] = OmniAuth::AuthHash.new(OmniAuthFixtures.facebook_mock)
end
```

That should take care of our tests. 

### Create a Facebook application and get the App Key and Secret

To create a Facebook application and obtain the application key and secret, you need to go through the following steps. 

* Navigate to [https://developers.facebook.com](https://developers.facebook.com)
* Click “Add new app” under “My Apps” menu item.
* Enter a name for your app and add a contact email.
* Add "Facebook Login" as a product but skip the "Quick Start" setup (we will configure the app manually)
* In "Settings -> Basic", add a valid URL (That Facebook can crawl) pointing to your Privacy Policy (Required if you want to make the application public. Not required if you'll use the app for development purposes only. You can set this to "https://craftacademy.se/privacy/" if you don't have a website policy page of your own)

* Enable "Client OAuth Login" and "Web OAuth Login"
* Add the callback URL ("Valid OAuth Redirect URIs") for your localhost to "http://localhost:3000/users/auth/facebook/callback"
* Navigate to the “App Review” section and set the “Make [APP NAME] public?” switch to “Yes”.
This is the minimal set up – there is much more you can do with your app, so feel free to visit other sections as well.
* Navigate back to "Settings -> Basic". Click “Show” next to the “App Secret” and enter your password to reveal the key. **You will need both the "App Key" and "App Secret" keys for the Devise initializer (see below)**


_**This setup will work for development purposes. If you want to go live with the app, you'll need to go through a more advanced setup. That is out of scope of this guide.**_

### Configure Devise Initializer

Back in your code base, locate the Devise initializer. You'll find it in `config/initializers`). In the scaffolded code (around line 260), you'll find some code examples for OAuth configuration (they are commented out). This is the place where we want to be and add our facebook credentials.

```ruby
  config.omniauth :facebook, 
                  APP KEY,
                  APP SECRET, 
                  {scope: 'email public_profile', callbackURL: '/auth/facebook/callback'}

```

The Scope is the list of permissions that we are requesting from the user upon authorization. `email` and `public_profile` scope means that only basic info will be available to our app and no actions on user’s behalf may be performed. 

The information returned from facebook will include:

* id
* first_name
* last_name
* middle_name
* name
* name_format
* picture
* short_name


There are loads of permissions that may be requested from the user, including ones that actually allow you to get access to sensitive data and do some actions on user’s behalf. Please [check the Open Graph API documentation](https://developers.facebook.com/docs/facebook-login/permissions/v3.0) for more information about what you need to do in order to access extended information and authorization.

_**Note: Never store your credentials in plain text if you are using version control. For development purposes, you might want to add the values right in the initializer, but you should look into enviromental variables or encrypted credentials to be on the safe side.**_

This is the same setup usinf Encrypted Credentials:

```ruby
  config.omniauth :facebook, 
                  Rails.application.credentials.facebook[:app_key],
                  Rails.application.credentials.facebook[:app_secret], 
                  {scope: 'email public_profile', callbackURL: '/auth/facebook/callback'}
```

At this stage, your tests should go green and you should be able to test the application manually if you fire up the local server and visit `http://localhost:3000` 














