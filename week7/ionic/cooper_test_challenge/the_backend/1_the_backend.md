**We will be using Ruby on Rails as the stack for our back-end. The challenge is to set up an API-only application that will make it possible to store information about users and their historical data.**

We will be using RSpec as out testing framework and PostgreSQL as our database.

Note that we **WILL NOT** be using Cucumber, so you don't have to install that framework. We are also using Rails *5.1.5* in this walkthrough, so make sure you have that installed on your computer.

Small exercise here for you, figure out how to install a specific version of a gem on your system ;-)

Let's go ahead and scaffold our application
```shell
rails _5.1.5_ new cooper_api --api --database=postgresql --skip-test --skip-bundle
```
This will do a couple of things for you:

- Configure your application to start with a limited set of middleware than normal.
- Make ApplicationController inherit from ActionController::API instead of ActionController::Base. As with middleware, this will leave out any Action Controller modules that provide functionalities primarily used by browser applications.
- Configure the generators to skip generating views, helpers and assets when you generate a new resource.
- --database=postgresql selects PostgreSQL as the database
- --skip-test option skips configuring for the default testing tool.
- --skip-bundle option prevents the generator from running bundle install automatically.
Next, update your `Gemfile` by going over it carefully and modifing it line by line, until you have the following:
```ruby
source 'https://rubygems.org'
git_source(:github) do |repo_name|
  repo_name = "#{repo_name}/#{repo_name}" unless repo_name.include?("/")
  "https://github.com/#{repo_name}.git"
end

ruby '2.4.1'

gem 'rails', '~> 5.1.5'
gem 'pg', '>= 0.18', '< 2.0'
gem 'puma', '~> 3.7'
gem 'jbuilder', '~> 2.5'

group :development, :test do
 gem 'pry-rails'
 gem 'pry-byebug'
end

group :development do
 gem 'listen', '~> 3.0.5'
 gem 'spring'
 gem 'spring-watcher-listen', '~> 2.0.0'
end
```
We also want to add the `rack`-cors gem to allow external clients to access our application. Add the dependency to your Gemfile.
```ruby
# Gemfile
# Use Rack CORS for handling Cross-Origin Resource Sharing (CORS),
# making cross-origin AJAX possible 
gem 'rack-cors', require: 'rack/cors'
```
Put something like the code below in `config/application.rb` of your Rails application. This will allow GET, POST, PUT and DELETE requests from any origin on any resource. This setting will also expose the appropriate headers and include them in the response, making it possible to authenticate users (Please go over the DeviseTokenAuth documentation for more details on HOW authentication using Tokens).
```ruby
# config/application.rb
module CooperApi
  class Application < Rails::Application
    # [...] other code...
    config.middleware.insert_before 0, Rack::Cors do
      allow do
        origins '*'
        resource '*', 
          headers: :any, 
          methods: %i[get post put delete],
          expose: %w(access-token expiry token-type uid client),
          max_age: 0
      end
    end
  end
end
```

**There are plenty of settings you can add to enhance the security of your application. Read about it in the `rack`-cors and `devise_token_auth` gem documentation.**

Next, we are now going to setup our testing framework.

Update your `Gemfile` with the following gems,
```ruby
group :development, :test do
  gem 'rspec-rails'
  gem 'shoulda-matchers'
  gem 'factory_bot_rails'
  # [...]
end
```
Remember to run bundle install every time you update your Gemfile.

Run `rails generate rspec:install` to install rspec for your rails project. Update the following file with respective code provided below:
```ruby
# spec/rails_helper.rb
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

```ruby
# spec/spec_helper.rb
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

```ruby
.rspec
--color
--require rails_helper
--format documentation
```
Create the following files:
```ruby
# spec/support/factory_bot.rb
RSpec.configure do |config|
  config.include FactoryBot::Syntax::Methods
end
```
```ruby
# spec/support/shoulda_matcher.rb
Shoulda::Matchers.configure do |config|
  config.integrate do |with|
    with.test_framework :rspec
    with.library :rails
  end
end

RSpec.configure do |config|
  config.include(Shoulda::Matchers::ActiveRecord, type: :model)
end
```
That's all for the setup. Next up, we will test-drive the creation of a test endpoint.