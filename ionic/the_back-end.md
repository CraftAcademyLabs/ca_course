# The Back-end - Step 2

We will be using Ruby on Rails as the stack for our back-end.

The challenge is to set up an API-only application that will make it possible to store information about users and their historical data.

We will be using RSpec as out testing framework and PostgreSQL as our database.

> Note that we **WILL NOT** be using Cucumber, so you don't have to install that framework. We are also using **Rails 5** in this walkthrough.

Let's go ahead and scaffold our application

```shell
rails new cooper_api --api --database=postgresql --skip-test --skip-bundle
```

This will do a couple of things for you:

* Configure your application to start with a limited set of middleware than normal.
* Make ApplicationController inherit from ActionController::API instead of ActionController::Base. As with middleware, this will leave out any Action Controller modules that provide functionalities primarily used by browser applications.
* Configure the generators to skip generating views, helpers and assets when you generate a new resource.
* `--database=postgresql` selects PostgreSQL as the database
* `--skip-test` option skips configuring for the default testing tool.
* `--skip-bundle` option prevents the generator from running bundle install automatically.

Next, update your `Gemfile` to have the following:

```ruby
source 'https://rubygems.org'
ruby '2.3.1'

gem 'rails', '~> 5.0.0', '>= 5.0.0.1'
gem 'pg', '~> 0.18'
gem 'puma', '~> 3.0'
gem 'jbuilder', '~> 2.5'

group :development, :test do
 gem 'pry'
 gem 'pry-byebug'
end

group :development do
 gem 'listen', '~> 3.0.5'
 gem 'spring'
 gem 'spring-watcher-listen', '~> 2.0.0'
end

```

We also want to add the `rack-cors` gem to allow external clients to access our application. Add the dependency to your `Gemfile`.

!FILENAME Gemfile

```ruby
# Use Rack CORS for handling Cross-Origin Resource Sharing (CORS),
# making cross-origin AJAX possible 
gem 'rack-cors', require: 'rack/cors'

```

Put something like the code below in `config/application.rb` of your Rails application. This will allow GET, POST, PUT and DELETE requests from any origin on any resource.

!FILENAME config\/application.rb

```ruby
module CooperApi
  class Application < Rails::Application
    # [...]
    config.middleware.insert_before 0, Rack::Cors do
      allow do
        origins '*'
        resource '*', headers: :any, methods: [:get, :post, :put, :delete]
      end
    end
  end
end
```

There are plenty of settings you can add to enhance security of your application. Read about it in the `rack-cors` and `devise_token_auth` gem documentation.

Next, we are now going to setup our testing framework.

Update your `Gemfile` with the following gems,

```ruby
group :development, :test do
  gem 'rspec-rails'
  gem 'shoulda-matchers'
  gem 'factory_girl_rails'
  # [...]
end
```

> Remember to run `bundle install` everytime you update your `Gemfile`.

Run `rails generate rspec:install` to install rspec for your rails project. Update the following file with respective code provided below:

* `spec/rails_helper.rb`

```ruby
ENV['RAILS_ENV'] ||= 'test'
require File.expand_path('../../config/environment', __FILE__)

abort('The Rails environment is running in production mode!') if Rails.env.production?
require 'spec_helper'
require 'rspec/rails'

ActiveRecord::Migration.maintain_test_schema!

Dir[Rails.root.join('spec/support/**/*.rb')].each { |f| require f }

RSpec.configure do |config|
  config.fixture_path = "#{::Rails.root}/spec/fixtures"
  config.use_transactional_fixtures = true
  config.infer_spec_type_from_file_location!
  config.filter_rails_from_backtrace!
end
```

* `spec/spec_helper.rb`

```ruby
RSpec.configure do |config|
  config.expect_with :rspec do |expectations|
    expectations.include_chain_clauses_in_custom_matcher_descriptions = true
  end

  config.mock_with :rspec do |mocks|
    mocks.verify_partial_doubles = true
  end

  config.shared_context_metadata_behavior = :apply_to_host_groups
end
```

* `.rspec`

```ruby
--color
--require rails_helper
```

Create the following files:

* `spec/support/factory_girl.rb`

```ruby
RSpec.configure do |config|
  config.include FactoryGirl::Syntax::Methods
end
```

* `spec/support/shoulda_matcher.rb`

```ruby
Shoulda::Matchers.configure do |config|
  config.integrate do |with|
    with.test_framework :rspec
    with.library :rails
  endend

RSpec.configure do |config|
  config.include(Shoulda::Matchers::ActiveRecord, type: :model)
end
```

That's all for the setup. Next up, we will test-drive the creation of a test endpoint.

### Testing APIs with RSpec

We will be using [request specs](https://www.relishapp.com/rspec/rspec-rails/v/3-5/docs/request-specs/request-spec) to test our api endpoints.

Let's create a dummy endpoint just to make sure everything is okay in terms of security settings.

Let's write our first spec to see if we can get a response from our endpoint.

In your `spec` folder create a folder named `requests`. Within that folder we need to add a folder structure that corresponds to the one we have in our `app/controllers` folder.

```shell
$ mkdir -p spec/requests/api/v0
$ mkdir spec/requests/api
$ mkdir spec/requests/api/v0

# or use the -p flag (stand for 'parents')
$ mkdir -p spec/requests/api/v0
```

Create a `ping_spec.rb` file and add the following code.

```
$ touch spec/requests/api/v0/ping_spec.rb
```

!FILENAME spec\/requests\/api\/v0\/ping\_spec.rb

```ruby
require 'rails_helper'

RSpec.describe Api::V0::PingController, type: :request do
  describe 'GET /v0/ping' do
    it 'should return Pong' do
      get '/api/v0/ping'

      json_response = JSON.parse(response.body)
      expect(response.status).to eq 200
      expect(json_response['message']).to eq 'Pong'
    end
  end
end
```

At this stage if we run this test, it will eventually fail. Let's work on getting that to pass.

> Note that if this is the first time you're running the test or the application, rails will throw you an error about non-existant database. Now would be a good time to set them up. Run `rails db:setup --all` then `rails db:migrate` to take care of this.

What we need to do next is create our `PingController`.

In the `app/controllers` folder, create the following folder structure.

```shell
$ mkdir app/controllers/api
$ mkdir app/controllers/api/v0
```

** **_**Note: The actual API routes will be placed in another namespace that we will call **_`V1`_**.**_** **

Inside that folder, we want to create our dummy controller.

```shell
$ touch app/controllers/api/v0/ping_controller.rb
```

We will let it inherit from our modified `ApplicationController`

!FILENAME app\/controllers\/api\/v0\/ping\_controller.rb

```ruby
class Api::V0::PingController < ApplicationController

end
```

If we run the test now, the error about undefined constant `Api::V0::PingController` is gone and RSpec should now throw you a `No route matches [GET] "/api/v0/ping"` error.

In your `routes.rb` create an API namespace and add V0 within it. Nested in that namespace we want to add a `:ping` resource with one single `:index` action.

!FILENAME config\/routes.rb

```ruby
Rails.application.routes.draw do
  namespace :api do
    namespace :v0 do
      resources :ping, only: [:index], constraints: { format: 'json' }
    end
  end
end
```

Run `rake routes` in your terminal to see if the route has been added properly.

With our route in place, the test now throws the following error

```shell
AbstractController::ActionNotFound:
  The action 'index' could not be found for Api::V0::PingController
```

In order to make this spec to pass, we need to add an `index` method to the `Api::V0::PingController`. When called, that method will respond with Json object with a single entry: `{message: 'Pong'}`.

!FILENAME app\/controllers\/api\/v0\/ping\_controller.rb

```ruby
class Api::V0::PingController < ApplicationController
  def index
    render json: { message: 'Pong' }
  end
end
```

Does it work? Fire up your server with `rails s` and visit `http://localhost:3000/api/v0/ping`

### Adding a User class

We know that we will be accessing our Rails app from an external client and that we will require authentication. At this point you are familiar with Devise - one of the most popular authentication libraries for Rails applications. We will be using `devise_token_auth` a token based authentication gem for Rails JSON APIs. It is designed to work well with `ng-token-auth` the token based authentication module for AngularJS.

As usual, we will be testing our units with RSpec and in order to make writing our specs a breeze, we will use `shoulda-matchers`, but this is probably old news for you at this stage. Again, if you need some pointers please go back in this documentation and revisit the [BDD with Rails](https://craftacademy.gitbooks.io/coding-as-a-craft/content/bdd_with_rails.html) chapter.

Make sure you install the `devise_token_auth` gem by adding it to your `Gemfile` and run `bundle install`. Note that you don't need to add `gem 'devise'` since `devise_token_auth` requires it automatically.

!FILENAME Gemfile

```ruby
gem 'devise_token_auth'
```

Using a generator that the gem provides, we can create a user model, define routes, etc.

Run the following command for an easy one-step installation.

```
$ rails g devise_token_auth:install User auth
```

Remember to migrate your database in order to create the `users` table (the Devise generator created an migration for you). But before you do that, make sure to open up the migration file and change the data type for Tokens to `text`.

!FILENAME db/migrate/XXX_devise_token_auth_create_users.rb

```ruby
## Tokens
t.text :tokens
```

In our User model (`app/models/user.rb`) we want to make sure that Devise is set up for our needs. We will remove the OAuth and Confirmation methods.

!FILENAME app/models/user.rb

```ruby
class User < ActiveRecord::Base
  # Include default devise modules.
  devise :database_authenticatable, :registerable,
          :recoverable, :rememberable, :trackable, :validatable
  include DeviseTokenAuth::Concerns::User
end
```

```
$ rails db:migrate --all
```

Another generator we need to run is a Factory generator for User. Generally, Rails generators invokes the Factory generators once that gem is installed, but not in the case of Devise Token Auth.

Run the generator from your terminal.

```
$ rails g factory_girl:model User email password password_confirmation
```

Make sure that the factory is properly configured with an valid email and password (if you don't you will get into trouble when validating the objects it creates).

!FILENAME spec/factories/users.rb

```ruby
FactoryGirl.define do
  factory :user do
    email 'user@random.com'
    password 'password'
    password_confirmation 'password'
  end
end
```

You can add more attributes to the User factory if you like, we added just the minimal required attributes at the moment.

Let's add a spec for the User factory we just created.

!FILENAME spec/models/user_spec.rb

```ruby
require 'rails_helper'

RSpec.describe User, type: :model do
  it 'should have valid Factory' do
    expect(FactoryGirl.create(:user)).to be_valid
  end
end
```

Now, we can add some basic model specs for User that will test the Devise setup.

!FILENAME spec\/models\/user\_spec.rb
```ruby
RSpec.describe User, type: :model do
  # [...]
  describe 'Database table' do
    it { is_expected.to have_db_column :id }
    it { is_expected.to have_db_column :provider }
    it { is_expected.to have_db_column :uid }
    it { is_expected.to have_db_column :encrypted_password }
    it { is_expected.to have_db_column :reset_password_token }
    it { is_expected.to have_db_column :reset_password_sent_at }
    it { is_expected.to have_db_column :remember_created_at }
    it { is_expected.to have_db_column :sign_in_count }
    it { is_expected.to have_db_column :current_sign_in_at }
    it { is_expected.to have_db_column :last_sign_in_at }
    it { is_expected.to have_db_column :current_sign_in_ip }
    it { is_expected.to have_db_column :last_sign_in_ip }
    it { is_expected.to have_db_column :confirmation_token }
    it { is_expected.to have_db_column :confirmed_at }
    it { is_expected.to have_db_column :confirmation_sent_at }
    it { is_expected.to have_db_column :unconfirmed_email }
    it { is_expected.to have_db_column :nickname }
    it { is_expected.to have_db_column :image }
    it { is_expected.to have_db_column :email }
    it { is_expected.to have_db_column :tokens }
    it { is_expected.to have_db_column :created_at }
    it { is_expected.to have_db_column :updated_at }
  end
end
```

We can also test some basic validations added by Devise.

!FILENAME spec/models/user_spec.rb

```ruby
RSpec.describe User, type: :model do
  #[...]
  describe 'Validations' do
    it { is_expected.to validate_presence_of(:email) }
    it { is_expected.to validate_confirmation_of(:password) }

    context 'should not have an invalid email address' do
      emails = ['asdf@ ds.com', '@example.com', 'test me @yahoo.com',
                'asdf@example', 'ddd@.d. .d', 'ddd@.d']

      emails.each do |email|
        it { is_expected.not_to allow_value(email).for(:email) }
      end
    end

    context 'should have a valid email address' do
      emails = ['asdf@ds.com', 'hello@example.uk', 'test1234@yahoo.si',
                'asdf@example.eu']

      emails.each do |email|
        it { is_expected.to allow_value(email).for(:email) }
      end
    end
  end
end
```

Okay, there will be plenty of opportunity to write more specs for the User model. But let's focus on adding some request specs to test our endpoints.

Next up, we need to add a new namespace to our `routes.rb` and move the generated Devise route into that namespace. We also want to tell Devise to skip `omniauth_callbacks`.

!FILENAME config/routes.rb

```ruby
Rails.application.routes.draw do
  namespace :api do
    # [...]

    namespace :v1 do
      mount_devise_token_auth_for 'User', at: 'auth', skip: [:omniauth_callbacks]
    end
  end
end
```

Now, we can run the `rails routes` command in the terminal to make sure we are set up correctly.

```
$ rake routes
                         Prefix Verb   URI Pattern                             Controller#Action
              api_v0_ping_index GET    /api/v0/ping(.:format)                  api/v0/ping#index {:format=>/(json)/}
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


### Testing the endpoints - request specs

First we need to add a helper method that will parse the server response body to JSON. Create a `support` folder in the `spec` folder. Add a new file named `response_json.rb`. In that file we will create a module that will parse the `response.body` to JSON and allow us to DRY out our request specs.

!FILENAME spec/support/response_json.rb

```ruby
module ResponseJSON
  def response_json
    JSON.parse(response.body)
  end
end

RSpec.configure do |config|
  config.include ResponseJSON
end
```


### User registration

Okay, let's write some specs for user registration.

```shell
$ mkdir -p spec/requests/api/v1
$ touch spec/requests/api/v1/registrations_spec.rb
```

!FILENAME spec/requests/api/v1/registrations_spec.rb

```ruby
RSpec.describe 'User Registration', type: :request do
  let(:headers) { { HTTP_ACCEPT: 'application/json' } }

  context 'with valid credentials' do
    it 'returns a user and token' do
      post '/api/v1/auth', params: {
        email: 'example@craftacademy.se', password: 'password',
        password_confirmation: 'password'
      }, headers: headers

      expect(response_json['status']).to eq 'success'      expect(response.status).to eq 200    end   end

  context 'returns an error message when user submits' do
    it 'non-matching password confirmation' do
      post '/api/v1/auth', params: {
        email: 'example@craftacademy.se', password: 'password',
        password_confirmation: 'wrong_password'
      }, headers: headers

      expect(response_json['errors']['password_confirmation'])
        .to eq ["doesn't match Password"]
      expect(response.status).to eq 422
    end

    it 'an invalid email address' do
      post '/api/v1/auth', params: {
        email: 'example@craft', password: 'password',
        password_confirmation: 'password'
      }, headers: headers

      expect(response_json['errors']['email']).to eq ['is not an email']
      expect(response.status).to eq 422    end

    it 'an already registered email' do
      FactoryGirl.create(:user, email: 'example@craftacademy.se',
                                password: 'password',
                                password_confirmation: 'password')

      post '/api/v1/auth', params: {
        email: 'example@craftacademy.se', password: 'password',
        password_confirmation: 'password'
      }, headers: headers

      expect(response_json['errors']['email']).to eq ['already in use']
      expect(response.status).to eq 422
    end
  end
end
```

The first spec is the happy path testing that user registration with the minimum of required fields works. The next specs are exposing the error messages we'll get if something goes wrong.

What other possible scenarios in the context of user registration should we test for?

### User authentication

Let's write some specs for logging in.

```shell
$ touch spec/requests/api/v1/sessions_spec.rb
```

!FILENAME spec/requests/api/v1/sessions_spec.rb

```ruby
RSpec.describe 'Sessions', type: :request do
  let(:user) { FactoryGirl.create(:user) }
  let(:headers) { { HTTP_ACCEPT: 'application/json' } }

  describe 'POST /api/v1/auth/sign_in' do
    it 'valid credentials returns user' do
      post '/api/v1/auth/sign_in', params: {
        email: user.email, password: user.password
      }, headers: headers

      expected_response = {
        'data' => {
          'id' => user.id, 'uid' => user.email, 'email' => user.email,
          'provider' => 'email', 'name' => nil, 'nickname' => nil,
          'image' => nil
        }      }

      expect(response_json).to eq expected_response
    end

    it 'invalid password returns error message' do
      post '/api/v1/auth/sign_in', params: {
        email: user.email, password: 'wrong_password'
      }, headers: headers

      expect(response_json['errors'])
        .to eq ['Invalid login credentials. Please try again.']
      expect(response.status).to eq 401
    end

    it 'invalid email returns error message' do
      post '/api/v1/auth/sign_in', params: {
        email: 'wrong@email.com', password: user.password
      }, headers: headers

      expect(response_json['errors'])
        .to eq ['Invalid login credentials. Please try again.']
      expect(response.status).to eq 401
    end
  end
end
```

### Add PerformanceData model

Let's add a way to store historical data for each user.

Run the following generator.

```
rails g model PerformanceData user:references data:hstore --force-plural
```

Open up the migration and add a line that adds `hstore` as a datatype and enables the database to store hashes.

!FILENAME db\/migrate\/XXXX\_create\_performance\_data.rb

```ruby
class CreatePerformanceData < ActiveRecord::Migration
  def change
    execute 'CREATE EXTENSION IF NOT EXISTS hstore'
    create_table :performance_data do |t|
      t.references :user, index: true, foreign_key: true
      t.hstore :data

      t.timestamps null: false
    end
  end
end
```

In your User model, add the following relationship.

!FILENAME app\/models\/user.rb

```ruby
class User < ActiveRecord::Base
  # [...]
  has_many :performance_data, class_name: 'PerformanceData'
end

```

Run the new migration.

```
$ rake db:migrate
```

Add the following specs.

!FILENAME spec\/models\/user\_spec.rb

```ruby

RSpec.describe User, type: :model do
  #[...]

  describe 'Relations' do
    it { is_expected.to have_many :performance_data }
  end

end
```

And to the newly created `spec/models/performance_data_spec.rb`.

!FILENAME spec\/models\/performance\_data\_spec.rb

```ruby

require 'rails_helper'

RSpec.describe PerformanceData, type: :model do

  describe 'Database table' do
    it { is_expected.to have_db_column :id }
    it { is_expected.to have_db_column :data }
  end

  describe 'Relations' do
    it { is_expected.to belong_to :user }
  end
end
```

### The controller

Let's create the  controller we will use to create, update and retrieve users historical data.

Create a new file in the following path: `app/controllers/api/v1/` Call it `performance_data_controller.rb` and add the class definition to it.

!FILENAME app\/controllers\/api\/v1\/performance\_data\_controller.rb

```ruby
class Api::V1::PerformanceDataController < ApplicationController

end
```

This is where we will be performing our CRUD actions.

The first thing we want to be able to do is to save data. 
Lets start by adding a `create` method to our new controller.

!FILENAME app\/controllers\/api\/v1\/performance\_data\_controller.rb

```ruby
class Api::V1::PerformanceDataController < ApplicationController
  def create

  end
end
```

We also need to create a route for that so let's modify our `routes.rb`  by adding a `post` route to it.

!FILENAME config\/routes.rb

```ruby
#[...]
namespace :v1 do
  mount_devise_token_auth_for 'User', at: 'auth', skip: [:omniauth_callbacks]
  post 'data', controller: :performance_data, action: :create, as: :create
end
#[...]
```

In your terminal, run `$ rake routes` to make sure everything is working. You should have a new route pointing to the `performance_data_controller.rb`'s `create` method.

```
api_v1_create POST   /api/v1/data(.:format)                api/v1/performance_data#create
```

So far so good...

Now, let's create a request spec and start adding functionality to our controller.

In the `spec/requests/api/v1/` folder we want to create a new test file. Let's call it `performance_data_spec.rb`. We can start with a simple test to see if our entry will be saved to the database \(it WILL fail at first, but that is the way we do it, right?\)

!FILENAME spec\/requests\/api\/v1\/performance\_data\_spec.rb

```ruby
require 'rails_helper'

describe 'Performance Data' do
  let(:headers) { {HTTP_ACCEPT: 'application/json'} }

  describe 'POST /api/v1/data/' do
    it 'creates a data entry' do
      post '/api/v1/data/', {performance_data: {data: {message: 'Average'}}}, headers
      entry = PerformanceData.last
      expect(entry.data).to eq({'message'=>'Average'})
    end

  end
end
```

In our controller we need to update the `create` method in order to get this test to pass.

!FILENAME  app\/controllers\/api\/v1\/performance\_data\_controller.rb

```ruby
# [...]
def create
  @data = PerformanceData.new(params[:performance_data])
  if @data.save
    render json: ({message: 'all good'})
  end
end
```

When you run the test now you will get an error with Rails complaining about `ForbiddenAttributesError`

    Performance Data
      POST /api/v1/data/
        creates a data entry (FAILED - 1)

    Failures:

      1) Performance Data POST /api/v1/data/ creates a data entry
         Failure/Error: @data = PerformanceData.new(params[:performance_data])

         ActiveModel::ForbiddenAttributesError:
           ActiveModel::ForbiddenAttributesError
         # ./app/controllers/api/v1/performance_data_controller.rb:4:in `create'
         ....

That is due to our params not being whitelisted. Some explanation is in place. Also do through [this resource about Action Controller](http://edgeguides.rubyonrails.org/action_controller_overview.html#parameters) to fully understand what is going on in the controllers we create and use in our application.

Action Controller parameters are forbidden to be used in Active Model mass assignments until they have been whitelisted. Strong Parameters provides an interface for protecting attributes from end-user assignment.

So we need to whitelist our params. There are many different approaches for doing that. For now we will whitelist all params contained on the `:performance_data` key and update the `create`method to use them.  Modify the `performance_data_controller.rb` with this code.

!FILENAME  app\/controllers\/api\/v1\/performance\_data\_controller.rb

```ruby
# [...]
def create
  @data = PerformanceData.new(performance_data_params)
  if @data.save
    render json: ({message: 'all good'})
  else
    render json: ({error: @data.errors.full_messages})
  end
end

private
def performance_data_params
  params.require(:performance_data).permit!
end
```

Note: We can use the `permit!` method for now, but there might be some security issues involved with that. Can you figure out a better way?

Okay, so that should work by now. The problem is that we are not assigning a `user` to the created entry.

What we want to do is to set the relationship between `PerformanceData` and `User` to `required`. Let's modify our `PerformaceData` model.

!FILENAME app\/models\/performance\_data.rb

```
class PerformanceData < ActiveRecord::Base
  belongs_to :user, required: true
end
```

If you run your request spec again, it will fail. The application does not know what user it should reference to the new object. .

We  need to update out controller to retrieve that information. We can do that with the built in Devise method `authenticate_user!`. We also need to add the user information to the `performance_data_params`. This can be done with a `merge!` command. Review the following code before you implement it in your `performance_data_controller.rb`.

!FILENAME  app\/controllers\/api\/v1\/performance\_data\_controller.rb

```ruby
class Api::V1::PerformanceDataController < ApplicationController
  before_action :authenticate_api_v1_user!

  def create
    @data = PerformanceData.new(performance_data_params.merge!({user: current_api_v1_user}))
    if @data.save
      render json: ({message: 'all good'})
    else
      render json: ({error: @data.errors.full_messages})
    end
  end

  private

  def performance_data_params
    params.require(:performance_data).permit!
  end
end
```

We also need to update our spec and create a user \(using Factory Girl\) and sent that users credentials with the request \(in headers\).  We will use the DeviseTokenAuth method `create_new_auth_token` to generate the necessary credentials. Make sure to use your debugger and break the test at some point to run this command on the user object manually to see it in action.

Anyway, your spec should look something like this.

!FILENAME spec\/requests\/api\/v1\/performance\_data\_spec.rb

```ruby
# [...]
let(:user) { FactoryGirl.create(:user) }
let(:headers) { {HTTP_ACCEPT: 'application/json'} }
let(:credentials) { user.create_new_auth_token }

describe 'POST /api/v1/data/' do
  it 'creates a data entry' do
    post '/api/v1/data/', {performance_data: {data: {message: 'Average'}}}, headers.merge!(credentials)
    entry = PerformanceData.last
    expect(entry.data).to eq({'message' => 'Average'})
  end
end
# [...]
```

If you run your specs now, you should not be getting any errors as the `PerformanceData` entry is connected to a `User` and no validation errors should be present.

You need, however, add some tests that hits the sad path and makes sure you have full control of what kind of error messages are being returned if the object you are trying to create fails validation.

Now, let us move on and create a method that will retrieve a collection of PerformanceData objects, but only for the user that makes the request. Let's start by setting the stage for a request spec and test if we get the right response.

!FILENAME spec\/requests\/api\/v1\/performance\_data\_spec.rb

```ruby
# [...]
describe 'GET /api/v1/data/' do
  before do
    5.times { user.performance_data.create(data: {message: 'Average'}) }
  end

  it 'returns a collection' do
    get '/api/v1/data/', {}, headers.merge!(credentials)
    expect(response_json['entries'].count).to eq 5
  end
end
```

Go ahead and run this spec just to get a friendly reminder that there is no such route as `GET /api/v1/data/`. Let's add that to our `routes.rb`

!FILENAME config\/routes.rb

```ruby
#[...]
namespace :v1 do
  #[...]
  get 'data', controller: :performance_data, action: :index, as: :index
end
#[...]
```

The next error you will see if you run the spec now, tells you that there is no `index` method defined in the controller. Let's create that.

!FILENAME  app\/controllers\/api\/v1\/performance\_data\_controller.rb

```ruby
class Api::V1::PerformanceDataController < ApplicationController
 # [...]
  def index
    @collection = current_api_v1_user.performance_data
    render json: ({entries: @collection})
  end
# [...]
end
```

We would need to add a few more specs to make sure that the `index` method works the way it is intended - to only return `PerformanceData` that belongs to the current user. But I'll leave that for you to add.

Note that we are NOT building any views or templates to display data. We MIGHT need more control of how the json response looks like and for that we will use the template capabilities of `JBuilder`. But for now, returning a rather uncomplicated json object will do.

That's it! We don't need to create the `update` and `delete` actions. There could be a use-case for them in the future, but at this stage they are not needed.

