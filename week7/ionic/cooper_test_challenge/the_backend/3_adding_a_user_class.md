## Adding a User class

**Okay, let's go to work and add some actual functionality to our application. In true Craft Academy style, we'll start with one of the hardest and complex parts.**

We know that we will be accessing our Rails app from an external client and that we will require authentication. At this point, you are familiar with [Devise](https://github.com/plataformatec/devise) - one of the most popular authentication libraries for Rails applications. We will be using `devise_token_auth` a [token-based authentication gem](https://github.com/lynndylanhurley/devise_token_auth) for Rails JSON APIs. It is designed to work well with `ng-token-auth` the token based authentication module for Angular.

As usual, we will be testing our units with RSpec and in order to make writing our specs a breeze, we will use `shoulda-matchers`, but this is probably old news for you at this stage. Again, if you need some pointers please go back in this documentation and revisit the [BDD with Rails](https://class.craftacademy.co/courses/course-v1:CraftAcademy+CA-CC-01+2018/courseware/bee39fc3856c4129b8a3986b6463257c/08c42816e7ed40b28b36f53f56003aed/?activate_block_id=block-v1%3ACraftAcademy%2BCA-CC-01%2B2018%2Btype%40sequential%2Bblock%4008c42816e7ed40b28b36f53f56003aed) chapter.

Make sure you install the `devise_token_auth` gem by adding it to your `Gemfile` and run `bundle install`. Note that you don't need to add gem `devise` since `devise_token_auth` requires it automatically.

```ruby
#Gemfile
gem 'devise_token_auth'
```
Using a generator that the gem provides, we can create a user model, define routes, etc.

Run the following command for an easy one-step installation.

```shell
$ rails g devise_token_auth:install User auth
```

Remember to migrate your database in order to create the `users` table (the Devise generator created a migration for you). But before you do that, make sure to open up the migration file and change the data type for `tokens` to `text`.

```ruby
#db/migrate/XXX_devise_token_auth_create_users.rb
## Tokens
t.text :tokens
```

In our User model (`app/models/user.rb`) we want to make sure that Devise is set up for our needs. We will remove the OAuth and Confirmation methods.

```ruby
# app/models/user.rb
class User < ActiveRecord::Base
  # Include default devise modules.
  devise :database_authenticatable, :registerable,
          :recoverable, :rememberable, :trackable, :validatable
  include DeviseTokenAuth::Concerns::User
end
```

```shell
$ rails db:migrate db:test:prepare
```

Another generator we need to run is a Factory generator for User. Generally, Rails generator invokes the Factory generators once that gem is installed, but not in the case of Devise Token Auth.

Run the factory generator from your terminal. (Yes, there IS a generator for factories!)

```shell
$ rails g factory_bot:model User email password password_confirmation
```

Make sure that the factory is properly configured with a valid email and password (if you don't you will get into trouble when validating the objects it creates).

```ruby
# spec/factories/users.rb
FactoryBot.define do
  factory :user do
    email 'user@random.com'
    password 'password'
    password_confirmation 'password'
  end
end
```

You can add more attributes to the User factory if you like, we added just the minimal required attributes at the moment.

Let's add a spec for the User factory we just created.

```ruby
# spec/models/user_spec.rb
require 'rails_helper'

RSpec.describe User, type: :model do
  it 'should have valid Factory' do
    expect(create(:user)).to be_valid
  end
end
```

Now, we can add some basic model specs for User that will test the Devise setup.

```ruby
# spec/models/user_spec.rb
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

```ruby
# spec/models/user_spec.rb
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

**Note: Examine all the specs in the 2 code blocks above before adding them to your own specs. If you have some previous experience using rspec , you'll notice that they are a bit different. In what way? What new ways of writing specs did we use here and are there any matchers used in the examples that you have not seen before?**

Okay, there will be plenty of opportunities to write more specs for the User model. But let's focus on adding some request specs to test our endpoints.

Next up, we need to add a new namespace to our `routes.rb` and move the generated Devise route into that namespace. We also want to tell Devise to skip `omniauth_callbacks`.

```ruby
# config/routes.rb
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

```shell
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