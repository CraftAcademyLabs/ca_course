This guide will provide a step-by-step instruction to scaffold a new Rails 5.2 application. At the time of writing this, Rails 5.2 is in Release Candidate 1, so we will use `rails new` command with the `--pre` flag.  We will be using a separate gemset to contain the dependencies used in this project. You should familiarize yourself with the concept of gemsets by reading more about them on the [RVM documentation](https://rvm.io/gemsets/basics) site. 

```
$ rvm gemset create rails_5_2
$ rvm gemset use rails_5_2
$ gem install rails --pre
$ rails new news_room --database=postgresql --skip-test --skip-bundle
$ cd news_room
```
* The --database=postgresql selects PostgreSQL as the database (The out-of-the-box setting is MySQL)
* The --skip-test option skips configuring for the default testing tool.
* The --skip-bundle option prevents the generator from running bundle install automatically.

Make sure to `cd` into the new project folder that the `rails new` command created for us. 

```
$ touch .ruby-gemset
```
Inside that file, we'll set the name of the gemset we want to use. This will automatically switch to the right gemset when you cd into the project folder.

```
rails_5_2
```

### HAML
In order to use Haml in your view templates, add `haml-rails` to your `Gemfile`. 


### Frameworks and tools

Add rspec-rails gem to the development and test groups of your Gemfile.

The RSpec extension library `shoulda-matchers` allows us to test common Rails functionality, like validations and associations, with less code.

The gem `factory_bot_rails` is a library for setting up Ruby objects as test data. It's essentially a fixtures replacement. It allows you to create objects that are needed in tests without providing a value for each required attribute. If you don't provide a value for a required attribute `factory_bot` will use the default value that you defined in factory's definition.

[Pry](https://github.com/pry/pry) is a powerful alternative to the standard IRB shell for Ruby. [`pry-rails`](https://github.com/rweng/pry-rails) causes Rails console to open pry. [`pry-byebug`](https://github.com/deivid-rodriguez/pry-byebug) adds step-by-step debugging and stack navigation capabilities to pry. 


```ruby
group :development, :test do
  gem 'factory_bot_rails'
  gem 'pry-byebug'
  gem 'pry-rails'
  gem 'rspec-rails'
  gem 'shoulda-matchers'
end
```

### Configuration

```ruby
module NewsRoom
  class Application < Rails::Application
    config.load_defaults 5.2
    config.generators do |generate|
      generate.helper false
      generate.assets false
      generate.view_specs false
      generate.helper_specs false
      generate.routing_specs false
      generate.controller_specs false
      generate.system_tests false
    end
  end
end
```

Open `spec/rails_helper.rb` and add the following block at the end of the file to configure ShouldaMatchers.

```ruby
Shoulda::Matchers.configure do |config|
  config.integrate do |with|
    with.test_framework :rspec
    with.library :rails 
  end
end
```

I also like to include FactoryBot methods in my RSpec config to be able to use shorter syntax in my specs:

```ruby
RSpec.configure do |config|
  # other settings 
  config.include FactoryBot::Syntax::Methods
end
```

Add Acceptance test framework

```ruby
group :development, :test do
  # Add Cucumber and Database Cleaner
  gem 'cucumber-rails', require: false
  gem 'database_cleaner'
end
```

Configure testing frameworks with default settings
```bash
$ bundle exec rails generate cucumber:install
$ bundle exec rails generate rspec:install
```

```bash
$ rails db:create db:migrate
```

Run your tests to check if all setup is working:

```bash
$ rake
```
If all is good, commit and push.