---
title: "Guides"
subtitle: "Implementing API OmniAuth with Facebook on web app"
author: [Craft Academy - Coding as a Craft]
date: Version 0.1
subject: "Guides, Facebook, Omniauth"
keywords: [Guides, Facebook, Omniauth]
titlepage: true
titlepage-color: f28e24
titlepage-text-color: "FFFFFF"
titlepage-rule-color: "FFFFFF"
titlepage-rule-height: 2
...

# Implementing API OmniAuth with Facebook on web app

This guide assumes that you have already implemented devise token auth and have setup correct routes for it with an API controller. 

First step is to add the `omniauth-facebook` gem to the Gemfile.
As always run bundle.

After this we are going to create the request spec for this feature.
Run this in the terminal:

`mkdir spec/support/`

`touch spec/support/oauth.rb`

`touch spec/requests/api/omniauth_spec.rb`

These files should look like this:

```ruby
# spec/requests/api/omniauth_spec.rb
require 'rails_helper'
RSpec.describe 'User Registration', type: :request do
  let(:headers) { { HTTP_ACCEPT: 'application/json' } }

  describe 'OmniAuth' do

    before do
      OmniAuth.config.test_mode = true
      OmniAuth.config.mock_auth[:facebook] = OmniAuth::AuthHash.new(OmniAuthFixtures.facebook_response)
    end

    context 'Facebook' do
      let(:params) { {omniauth_window_type: 'newWindow'} }

      let(:request) { lambda do
        get('/api/auth/facebook/',
        params: params,
        headers: headers)
        follow_redirect! until response.status == 200
      end }

      let(:bad_request) { lambda do
        get('/api/auth/facebook/',
        params: params,
        headers: headers)
      end }

      it 'allows user to register with valid authorization' do
        # Uses default mock, set in support/oauth.rb
        expect { request.call }.to change(User, :count).by(1)
      end

      it 'fails to register user with invalid authorization' do
        OmniAuth.config.mock_auth[:facebook] = :invalid_credentials
        expect { bad_request.call }.to change(User, :count).by(0)
      end
    end
  end
end
```

```ruby
# spec/support/oauth.rb
module OmniAuthFixtures
    def self.facebook_response
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

You will also need to add a line of code to the `rails_helper.rb` so that the request spec can see the support file.

```ruby
# spec/rails_helper.rb

# [...] other code...
Dir[Rails.root.join('spec/support/**/*.rb')].each { |f| require f }
# [...] other code...
```

If you run the test now you will get an error that states that you have no route matches. So lets setup the routes for this. As said before I assume in this guide that you have implemented devise token auth already. If not go to the other guide about implementing this on an web app and finish that one before continuing on this one. The API namespace in `routes.rb` should look like this:

```ruby
Rails.application.routes.draw do
  namespace :api do
    mount_devise_token_auth_for 'User', at: 'auth', controllers: {
      sessions: 'api/sessions',
      registrations: 'api/registrations',
      omniauth_callbacks: 'api/omniauth_callbacks'
    }
  end
 # [...] other code...
end
```

So as you can see we are pointing to a `omniauth_callbacks` controller. This one does not exist yet, so lets create one. 
Run this in the terminal:

`touch app/controllers/api/omniauth_callbacks_controller.rb`

The file should look like this:
```ruby
class Api::OmniauthCallbacksController < DeviseTokenAuth::OmniauthCallbacksController

  def omniauth_success
    get_resource_from_auth_hash
    set_token_on_resource
    create_auth_params

    sign_in(:user, @resource, store: false, bypass: true)
    @resource.save!

    yield @resource if block_given?

    render_data_or_redirect('deliverCredentials', @auth_params.as_json, @resource.as_json)
  end

  def omniauth_failure
    @error = params[:message]
    render_data_or_redirect('authFailure', {error: @error})
  end
end
```

If you run the test now, the sad path will go green. We still get an routing error on the other test. Its not the same one as before. This one is complaining because we have not set the provider yet. Run this in the terminal:

`touch config/initializers/omniauth.rb`

This is where you will later put the facebook credentials from your developer facebook application. But for now we will make it look like this:
```ruby
Rails.application.config.middleware.use OmniAuth::Builder do
  provider :facebook
end
```

If you run the tests again you will now see that we get all greens. But to get this to actually work with a client we need to add some code to the user model.
```ruby
class User < ApplicationRecord
  devise :database_authenticatable, :registerable,
         :recoverable, :rememberable, :trackable, :validatable

  include DeviseTokenAuth::Concerns::User

  devise :omniauthable, omniauth_providers: [:facebook]

  private

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

It is very importand that you put `devise :omniauthable, omniauth_providers: [:facebook]` underneath `include DeviseTokenAuth::Concerns::User`. Otherwise you will get annoying errors.

So now you have rails backend with working omniauth Facebook login.
