# Implementing devise token auth on web app
This guide assumes that you have already have a web application with devise login successfully implemented and now want to add this functionality to an mobile client. 

In this guide we will go through how to setup the Rails application to also act as an API and add Devise token auth so users can get authorised through the API. 

The first thing we need to do is that we need to add some gems to our Gemfile
```
# Gemfile
´´´
gem 'devise_token_auth'
gem 'rack-cors', require: 'rack/cors'
´´´
```
The `rack-cors` gem makes our application allow external clients to access it.

`devise_token_auth` is what we are going to use to make it possible to login with the API, it works well with ionic and we are already familiar with devise from our previous projects.

Open up `config/aplplication.rb` so we can make some changes. The changes we are going to make here is so the `rack-cors` gem can do its job. We will allow GET, POST, PUT and DELETE requests from any origin here. 

```ruby
# config/application.rb
module YourApplicationName
  class Application < Rails::Application
    # [...] other code...
    config.middleware.insert_before 0, Rack::Cors do
      allow do
        origins '*'
        resource '*', headers: :any, methods: [:get, :post, :put, :delete]
      end
    end
  end
end
``` 

Now we are going to update the User model with some new attributes. Start off with generating a new migration. 
Run `rails generate migration DeviseTokenAuthUpdateUsers`.
This will give you a new and almost empty migration file in the `db/migrate` folder. 
Update that file so it looks like this:
```ruby
class DeviseTokenAuthUpdateUsers < ActiveRecord::Migration[5.2]
  def change
    add_column :users, :provider, :string, null: false, default: 'email'
    add_column :users, :uid, :string, null: false, default: ''
    add_column :users, :tokens, :text
    add_column :users, :confirmation_token, :string
    add_column :users, :confirmed_at, :datetime
    add_column :users, :confirmation_sent_at, :datetime

    User.reset_column_information
    User.find_each do |user|
      user.uid = user.email
      user.provider = 'email'
      user.save!
    end

    add_index :users, [:uid, :provider], unique: true
  end
end
```
After this run `rails db:migrate`

Now lets add the requests specs for both login/logout and registration. 
```ruby
#spec/requests/api/registrations_spec.rb
require 'rails_helper'

RSpec.describe 'User Registration', type: :request do
  let(:headers) { { HTTP_ACCEPT: 'application/json' } }

  context 'with valid credentials' do
    it 'returns a user and token' do
      post '/api/auth', params: {
        email: 'me@mail.com', password: 'whatever',
        password_confirmation: 'whatever'
      }, headers: headers

      expect(JSON.parse(response.body)['status']).to eq 'success'
      expect(response.status).to eq 200
    end
  end

  context 'returns an error message when user submits' do
    it 'non-matching password confirmation' do
      post '/api/auth', params: {
        email: 'me@mail.com', password: 'whatever',
        password_confirmation: 'Whatever'
      }, headers: headers

      expect(JSON.parse(response.body)['errors']['password_confirmation']).to eq ["doesn't match Password"]
      expect(response.status).to eq 422
    end

    it 'an invalid email address' do
      post '/api/auth', params: {
        email: 'me@mail', password: 'whatever',
        password_confirmation: 'whatever'
      }, headers: headers

      expect(JSON.parse(response.body)['errors']['email']).to eq ['is not an email']
      expect(response.status).to eq 422
    end

    it 'an already registered email' do
      FactoryBot.create(:user, email: 'me@mail.com',
                    password: 'whatever',
                    password_confirmation: 'whatever')

      post '/api/auth', params: {
        email: 'me@mail.com', password: 'whatever',
        password_confirmation: 'whatever'
      }, headers: headers

      expect(JSON.parse(response.body)['errors']['email']).to eq ['has already been taken']
      expect(response.status).to eq 422
    end
  end
end
```
```ruby
# spec/requests/api/sessions_spec.rb
require 'rails_helper'

RSpec.describe 'Sessions', type: :request do
  let(:user) { FactoryBot.create(:user) }
  let(:headers) { { HTTP_ACCEPT: 'application/json' } }

  describe 'POST /api/auth/sign_in' do
    it 'valid credentials returns user' do
      post '/api/auth/sign_in', params: {
        email: user.email, password: user.password
      }, headers: headers

      expected_response = {
        'data' => {
          'id' => user.id, 'uid' => user.email, 'email' => user.email,
          'provider' => 'email'
        }    
      }

      expect(JSON.parse(response.body)).to eq expected_response
    end

    it 'invalid password returns error message' do
      post '/api/auth/sign_in', params: {
        email: user.email, password: 'wrong_password'
      }, headers: headers

      expect(JSON.parse(response.body)['errors'])
        .to eq ['Invalid login credentials. Please try again.']
      expect(response.status).to eq 401
    end

    it 'invalid email returns error message' do
      post '/api/auth/sign_in', params: {
        email: 'wrong@email.com', password: user.password
      }, headers: headers

      expect(JSON.parse(response.body)['errors'])
        .to eq ['Invalid login credentials. Please try again.']
      expect(response.status).to eq 401
    end
  end
end
```
If you run the test now, you will see that we don´t have any route matches

Lets start off with the routes then, add these lines to the `config/routes.rb`:
```ruby
# config/routes.rb
Rails.application.routes.draw do
  namespace :api do
    mount_devise_token_auth_for 'User', at: 'auth', skip: [:omniauth_callbacks]
  end
end
```

Now, we can run the  `rails routes`  command in the terminal to make sure we are set up correctly.

```

$ rails routes
                         Prefix Verb   URI Pattern                             Controller#Action
              api_v0_pings      GET    /api/v0/pings(.:format)                 api/v0/pings#index {:format=>/(json)/}
        new_api_v1_user_session GET    /api/v1/auth/sign_in(.:format)          devise_token_auth/sessions#new
            api_v1_user_session POST   /api/v1/auth/sign_in(.:format)          devise_token_auth/sessions#create
    destroy_api_v1_user_session DELETE /api/v1/auth/sign_out(.:format)         devise_token_auth/sessions#destroy
           api_v1_user_password POST   /api/v1/auth/password(.:format)         devise_token_auth/passwords#create
       new_api_v1_user_password GET    /api/v1/auth/password/new(.:format)     devise_token_auth/passwords#new
      edit_api_v1_user_password GET    /api/v1/auth/password/edit(.:format)    devise_token_auth/passwords#edit
                                PATCH  /api/v1/auth/password(.:format)         devise_token_auth/passwords#update
                                PUT    /api/v1/auth/password(.:format)         devise_token_auth/passwords#update
cancel_api_v1_user_registration GET    /api/v1/auth/cancel(.:format)           devise_token_auth/registrations#cancel
       api_v1_user_registration POST   /api/v1/auth(.:format)                  devise_token_auth/registrations#create
   new_api_v1_user_registration GET    /api/v1/auth/sign_up(.:format)          devise_token_auth/registrations#new
  edit_api_v1_user_registration GET    /api/v1/auth/edit(.:format)             devise_token_auth/registrations#edit
                                PATCH  /api/v1/auth(.:format)                  devise_token_auth/registrations#update
                                PUT    /api/v1/auth(.:format)                  devise_token_auth/registrations#update
                                DELETE /api/v1/auth(.:format)                  devise_token_auth/registrations#destroy
       api_v1_user_confirmation POST   /api/v1/auth/confirmation(.:format)     devise_token_auth/confirmations#create
   new_api_v1_user_confirmation GET    /api/v1/auth/confirmation/new(.:format) devise_token_auth/confirmations#new
                                GET    /api/v1/auth/confirmation(.:format)     devise_token_auth/confirmations#show
     api_v1_auth_validate_token GET    /api/v1/auth/validate_token(.:format)   devise_token_auth/token_validations#validate_token
```

Now we need an API controller, lets create one. Run `touch app/controllers/api_controller.rb` in the terminal and add this: 
```ruby
class ApiController < ActionController::API
  include DeviseTokenAuth::Concerns::SetUserByToken
end
```

Add this to the User model `include DeviseTokenAuth::Concerns::User`. The file should look something like this:

```ruby
class User < ApplicationRecord
  devise :database_authenticatable, :registerable,
         :recoverable, :rememberable, :trackable, :validatable

  include DeviseTokenAuth::Concerns::User
end
```

This should make all the tests go green.

Now when all the test go green it is time to actually test the API with postman. When you test to sign in or login it will give you an error saying `Can't verify CSRF token authenticity.`
To fix this you need to run `mkdir app/controllers/api`
`touch app/controllers/api/sessions_controller.rb`
`touch app/controllers/api/registrations_controller.rb`

Make the two files look like this
```ruby
class  Api::SessionsController  <  DeviseTokenAuth::SessionsController
  skip_before_action :verify_authenticity_token
end
```

```ruby
class  Api::RegistrationsController  <  DeviseTokenAuth::RegistrationsController
  skip_before_action :verify_authenticity_token
end
```

In our `config/routes.rb` file we need to make sure that it looks for our controllers we created. Make it look like this
```ruby
Rails.application.routes.draw do
  # [...] other code...
  namespace :api do
    mount_devise_token_auth_for 'User', at: 'auth', skip: [:omniauth_callbacks], controllers: {
      sessions: 'api/sessions',
      registrations: 'api/registrations'
    }
  end
end
```

If you try testing it with postman again it should all work now. 
