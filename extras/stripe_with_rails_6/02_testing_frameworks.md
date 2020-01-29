## Testing Frameworks and tools

Add `rspec-rails` gem to the development and test groups of your Gemfile.

The RSpec extension library `shoulda-matchers` allows us to test common Rails functionality, like validations and associations, with less code.

The gem `factory_bot_rails` is a library for setting up Ruby objects as test data. It's essentially a fixtures replacement. It allows you to create objects that are needed in tests without providing a value for each required attribute. If you don't provide a value for a required attribute `factory_bot` will use the default value that you defined in factory's definition.

```ruby
group :development, :test do
  gem 'factory_bot_rails'
  gem 'rspec-rails'
  gem 'shoulda-matchers'
end
```

### Configuration

_**config/application.rb**_

```ruby
module MyProject
  class Application < Rails::Application
    config.load_defaults 6.0
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
