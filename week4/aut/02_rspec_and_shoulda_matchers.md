Add `rspec-rails` gem to the `development` and `test` groups of your Gemfile.

The RSpec extension library `shoulda-matchers` allows us to test common Rails functionality, like validations and associations, with less code.

The gem `factory_bot` is a library for setting up Ruby objects as test data. It's essentially a fixture replacement. It allows you to create objects that are needed in tests without providing a value for each required attribute. If you don't provide a value for a required attribute factory_bot will use the default value that you defined in the factory's definition.

    group :development, :test do
      gem 'rspec-rails'
      gem 'shoulda-matchers'
      gem 'factory_bot_rails'
    end

Now, we can install all the new dependencies with `bundle install`.

    $ bundle install

Run the RSpec generator to add the testing framework to your rails application

    $ bundle exec rails generate rspec:install

Why `bundle exec` before a command? `bundle exec` executes a command in the context of your bundle. That means it uses the gems specified in your Gemfile. It standardizes the environment under which the program runs. This helps avoid version hell and makes life much easier.

Before you can use `shoulda-matchers`, you need to configure it by choosing the test framework and features of `shoulda-matchers` you want to use.

Open `spec/rails_helper.rb` and add the following block.

    Shoulda::Matchers.configure do |config|
      config.integrate do |with|
        with.test_framework :rspec
        with.library :rails 
      end
    end

Before we move on we need to add another configuration to the Rails application to avoid the generators to scaffold too many files. Make the following modification to the `config/application.rb` file:

    class Application < Rails::Application
      # Disable generation of helpers, javascripts, CSS, and view, helper, routing and controller specs
      config.generators do |generate|
        generate.helper false
        generate.assets false
        generate.view_specs false
        generate.helper_specs false
        generate.routing_specs false
        generate.controller_specs false
      end
      # ...
    end

One last thing in this setup process, open the `.rspec` file (it is located in your main project folder but it is a hidden file, so your text editor might not show it) and modify it so that the first line is set to:

    --format documentation

Now, in your terminal, type in `bundle exec rspec` and hit enter. The output you see should be something like:

    $ bundle exec rspec
    No examples found.

    Finished in 0.00023 seconds (files took 0.5029 seconds to load)
    0 examples, 0 failures