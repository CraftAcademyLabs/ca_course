---
title: "OAuth in Rails"
subtitle: "Authenticating User With Multiple OAuth Providers"
author: [Craft Academy - Coding as a Craft]
date: Version 0.1
subject: "OAuth, Authentication, Rails"
keywords: [OAuth, Authentication, Rails]
titlepage: true
titlepage-color: f28e24
titlepage-text-color: "FFFFFF"
titlepage-rule-color: "FFFFFF"
titlepage-rule-height: 2
...

## Authenticating User With Multiple OAuth Providers 

This Guide assumes that you have a Rails (5.2) application up and running with OmniAuth authentication using Facebook configured (please see "OAuth with Facebook" guide).

### The Acceptance test

In the previous guide, we wrote an acceptance test that layed out the relatively hight-level goal of the feture.


```gherkin
Feature: User can log in using hers/his Facebook credentials
    As a user of the system
    In order to simplify the sign up/sign in process 
    I would like to be able to authenticate myself using Facebook 


Scenario: Visitor clicks on 'Login with Facebook' and gets authenticated
    Given I visit the site
    And I click "Login with Facebook"
    Then I should be redirected to the index page
    And I should see "Successfully authenticated from Facebook account" 
```

Now, we want to expand on that functionality, and allow the user to choose another OAuth provider. Let's say Google+. 

We would like to modify the user story a bit.

```
As a user of the system
In order to simplify the sign up/sign in process 
I would like to be able to authenticate myself using a well known service where I have an account. 
```

With that story in place, we can add another scenario, where we simulateauthenticating and logging in with Google+:

```gherkin
Scenario: Visitor clicks on 'Login with Google+' and gets authenticated
    Given I visit the site
    And I click "Login with Google"
    Then I should be redirected to the index page
    And I should see "Successfully authenticated from Google account" 
```

Running this scenario will render a `Capybara::ElementNotFound` error. At this stage you know how to use automated tests to help you in development so I won't be covering every error message you'll encounter. Also, in this story we will use this high-level scenari as a final validation that our implementation works. Since we are dealing with functionality that requires thisr-party api's, we will need to do some manual testing. 

Okay, so Capybara can't find our link. Somewhere on your view, you need to place the  "Login with Google" link or button. But do we have a url to point this link to? We do not - yet. Let's fix that and add a route (url) to the Google+ login to our routes table.

Again, most of the setup is already in place if you have followed the first guide in this series and configured the Facebook login. Now, open your `User` model and add `:google` to the list of OAuth Providers. It should look something like this:

```ruby 
devise :omniauthable, omniauth_providers: [:facebook, :google_oauth2]
```

That will give you two new routes. If you examine your route at this point, you should see them pointing to the `OmniauthCallbacksController`:

```
user_google_oauth2_omniauth_authorize GET|POST /users/auth/google_oauth2(.:format)                                                      omniauth_callbacks#passthru
 user_google_oauth2_omniauth_callback GET|POST /users/auth/google_oauth2/callback(.:format)                                             omniauth_callbacks#google_oauth2
```

Let's grab the authorization path and setup the "Login with Google" link on your page. 

```haml
= link_to "Login with Google", user_google_oauth2_omniauth_authorize
```

If you run the scenario now, you will probably end up with an `ExpectationNotMetError`. You are NOT being re-routet to the path you expected. 

```
expected: "/"
got: "/users/auth/google_oauth2"
```

That's perfectly normal. In test enviroment, we can not perform the authentication. We need to stub out that call and return some mock data. As we did in the with Facebook in the first iteration of this feature.

## Stub and Mock

If you've followed the testing strategy we used in the first guide in this series, you have your `OmniAuthFixtures` module configured (in the `spec/support/` folder). We will expand that module and include a scaffolded Google+ response that we will use in our test enviroment. 

```ruby
def self.google_oauth2_response
    {
      provider: 'google_oauth2',
      uid: '123456789101112130122',
      info: {
        email: 'joe@bloggs.com',
        first_name: 'Joe',
        image: "https://robohash.org/sitsequiquia.png?size=512x512",
        last_name: "Bloggs",
        name: "Joe Bloggs",
        urls: { Google: "https://plus.google.com/494850544950524948535348525457565050575557" }
      },
      credentials: {
        expires: true,
        expires_at: 1450062184,
        refresh_token: "XYZW...",
        token: "ABCDEF..."
      },
      extra: {
        id_info: {
          at_hash: 'abcdefg',
          aud: "50906091245.apps.googleusercontent.com",
          azp: "50906091245.apps.googleusercontent.com",
          email: "joe@bloggs.com",
          email_verified: true,
          exp: 1450062184,
          iat: 1450058584,
          iss: "accounts.google.com",
          sub: "102612410550469822979"
        },
        id_token: "abcdefghijklmnopqrstuvwxyz",
        raw_info: {
          email: "joe@bloggs.com",
          email_verified: "true",
          family_name: "Bloggs",
          gender: "male",
          given_name: "Joe",
          kind: "plus#personOpenIdConnect",
          locale: "en",
          name: "Joe Bloggs",
          picture: "https://robohash.org/sitsequiquia.png?size=512x512",
          profile: "https://plus.google.com/494850544950524948535348525457565050575557",
          sub: "494850544950524948535348525457565050575557"
        },
      }
    }
  end

``` 

The way we will use this response is very similar to the facebook moch we used in the first guide:

```ruby
Before do
  OmniAuth.config.test_mode = true
  OmniAuth.config.mock_auth[:facebook] = OmniAuth::AuthHash.new(OmniAuthFixtures.facebook_response)
  # We need to add a mock for the `google_oauth2` provider.
  OmniAuth.config.mock_auth[:google_oauth2] = OmniAuth::AuthHash.new(OmniAuthFixtures.google_response)
end

```

At this point we will get some other errors that has to do with the fact that we haven't configured the Google OAuth strategy.

Let's start with including the necessary library (gem) to our `Gemfile`

```
gem 'omniauth-google-oauth2', '~> 0.5.3'
```

We also need to configure the Devise initializer. To start with, let's add a very basic cfiguration:

```ruby 
  config.omniauth :google_oauth2, 12345, 12345 #where the first value is the app id and the second the application secret.
```

Or, if you use the Middleware solution, modify the initializer with the settings for your Googla App. (The example below includes the setting for Rails encrypted credentials)

```ruby
Rails.application.config.middleware.use OmniAuth::Builder do

  provider :google_oauth2, 
  Rails.application.credentials.facebook[:google_key],
  Rails.application.credentials.facebook[:google_secret]

  provider :facebook,
  Rails.application.credentials.facebook[:facebook_key],
  Rails.application.credentials.facebook[:facebook_secret]
end
```

At this stage, if you keep runnig the scenapri, you will notice that there is a method missing in your `OmniauthCallbacksController` and the callback is failing. 

You need to add a method with the same name as the provider source. 

```ruby
# app/controllers/omniauth_callbacks_controller.rb

def google_oauth2
    @user = User.from_omniauth(request.env['omniauth.auth'])
    params = request.env["omniauth.params"]
    if @user.persisted?
      sign_in_and_redirect @user, event: :authentication
      set_flash_message(:notice, :success, kind: 'Google') if is_navigational_format?
    else
      session["devise.google_data"] = resquest.env('omniauth.auth')
      redirect_to new_user_registration_path #maybe not
    end
  end
``` 

At this stage, your tests should go green. 

## Refactoring

If we examine the 2 methods in the  `OmniauthCallbacksController` for the two providers, we can clerly see a lot of code repetition. We would benefit from bundling them together to DRY out our code. 

The exapmple below makes use of method name aliases and the built in Ruby methods `__callee__`, a variant of the method `__method__` that can be used to, at runtime, get the name of the method that has been invoked. We can use that name to made the necassary modification to the code where the provider names are being used. 

```ruby
  def all
    @user = User.from_omniauth(request.env['omniauth.auth'])
    params = request.env["omniauth.params"]
    I18n.locale = params["locale"]
    if @user.persisted?
      sign_in_and_redirect @user, event: :authentication
      set_flash_message(:notice, :success, kind: __callee__.to_s.split('_').first.titleize) if is_navigational_format?
    else
      session["devise.#{__callee__.to_s.split('_')}_data"] = resquest.env('omniauth.auth')
      redirect_to new_user_registration_path #maybe not
    end
  end
  [:facebook, :google_oauth2].each do |provider|
    alias_method provider, :all
  end

```

## Wrap up
With this setup your users will be presented with another way to register an account. This process should be flexible enough to add even more providers like Twitter or GitHub.





